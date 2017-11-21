# Designing Pipeline
We've discussed the setup of data storage and the base object detection model as separate parts. Generally, we will use s3 for storage, and then process the object detection on images using python scripts with Tensorflow to get some output. This writeup introduces the design of our core pipeline, in which we connect every piece we have to form an automated process to actually generate business value.

## Building Blocks
Our pipeline will be broken into three major parts, which are preprocessing, object detection, and postprocessing. Of course, you could lump them into one piece, but there are several reasons not to do that. First, for a system like in our case, we might want to treat data from each user differently, and to process data from multiple users parallelly. Splitting up steps allows us to scale each step differently. Second, different steps require different tools, split up steps provide the freedom to use fewer tools for a particular step.

The Pachyderm will data versioning for each repo of the pipeline, so each time we make a copy of a file, it just makes incremental changes and then creates a pointer rather than an actual copy to take up more space. Thus, we don't have to build an additional repository for keeping historical records.
### Preprocessing - Data Validation
The input of this pipeline is no doubt images. Since our team is building a data pipeline for a commercialized security system, its reasonable to assume those images are coming from different cameras of different properties. As a result, we should not treat all of them the same way for the whole process. How those images are generated and collected is out of the boundary of our pipeline.

Before we do object detection, we need to preprocess our inputs to ensure that the downstream processing happens only on qualified inputs rather than wasting time on garbage. We need some rules for input validation, and the same set of rules applies to all the inputs regardless of their sources. When we generate the alert, we want to alert a specific user or group of users, so it’s important that we have an existing tag recording when and where (in terms of a user or device) is the image coming from at the beginning. In other words, we need at least a user id and a timestamp to indicate the location and time of each image, and our team decided the tag be in the format of "id_timesatmp.jpg" as image names. Specifically, we will use [UNIX timestamp](https://en.wikipedia.org/wiki/Unix_time) and the time zone is set to be UTC. If you want to replicate our system, you can define your own tag rules, and tags can actually in many other formats, which is really flexible. For example, you can have a separate text file for each image, or embed tags in the image and extract them later. However, you should always pay close attention to the track of time zone, especially if the system is planned to work on multilocation. Besides, we expect our inputs to be colored. We might also add rules on resolution if we have time, because poor resolution could cause trouble in detection. For this part, you can also enforce things like format of image or size of image based on your model design. The detection model we will use here is quite flexible on size and format, so we are not going to restrict that. 

If officially put into business, we will have convention contract based on those rules, so that all images sent to this system for process are supposed to comply with the rules. But if you are processing from certain data sources which already have had their data conventions, you may want to build your pipeline according to their policy. Also, we will document the dataset we trained our model on to let users know objects they can expect to be detected.

Each image after validation process will be categorized either valid or invalid. In our case, we will discard all the invalid images without any further processing, like doing some enhancement, but we do plan to keep them separately in case there are problems requiring us to go back to those images to debug. For example, if we do not receive any threats report for a device over an unusually long time, we need those data to figure out if the problem lies with the device or our process settings.
### Object Detection
With input standardized, the next step is to feed valid images into the object detection model we described in the previous writeup. Output from here is the class detected, which can be in the form of image or text representations, such as a JSON file. To carry through the identity of the image, we will name the JSON file in the "id_timesatmp.json". The reason we have a JSON file to record the detected classes and corresponding probabilities in addition to marks on images is that it’s easier to automate around text representations than images with box.
### Postprocessing - Threat Detection
After the object detection, we need one more detection process to determine the threat level, which we will call it threat detector. In our design, for this step we will process by ID. That is to say we will bucket everything from each ID together, and process things under the same ID (folder) the same way through a loop. As you know, the definition of threats divers among users, so processing by ID is the most efficient way. The detector will take in the JSON files outputted from the last step, read in corresponding threats rules, a list of dangerous classes required by the specific user, and match objects against the rules. You do not have to document anything about classes that are not dangerous. Any detected threats will be written back into the JSON file, and the JSON file will be saved as a copy into another data repository.
### Postprocessing - Alert Service
To get an image with threat detected is not the end of our pipeline. The ultimate goal should be a notification to the specific user and/or the police when a threat is detected. We will realize this in email delivery via API. We choose to make a JSON API call with image and text to the SMTP API of Sendgrid, an email alert service company. Other email service options include Nagios and Pagerduty.
### Postprocessing - Monitoring Plot
Besides customers, as a security service company, we also want to maintain an internal visibility about some key statistics, such as where and how things are triggered, as well as the frequency. We decided to generate a bar plot per day to get a general idea. Locations on the plot is corresponded to IDs, and the value would be the count of the files under the certain folder within the period of time. Another reason why tags with ID and time-zone-identified timestamp are essential. Other statistics can be generated and analyzed based on a similar mechanism, so a dashboard is also possible if you justify the need.
