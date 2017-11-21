# Designing Pipeline
We've discussed the setup of data storage and the base object detection model as separate parts. Generally, we will use s3 for storage, and then process the object detection on images using python scripts with Tensorflow to get some output. This writeup introduces the design of our core pipeline, in which we connect every piece we have to form an automated process to actually generate business value.

## Building Blocks
Our pipeline will be broken into three major parts, which are preprocessing, object detection, and postprocessing. Of course, you could lump them into one piece, but there are several reasons not to do that. First, for a system like in our case, we might want to treat data from each user differently, and to process data from multiple users parallelly. Splitting up steps allows us to scale each step differently. Second, different steps require different tools, split up steps provide the freedom to use fewer tools for a particular step.

### Preprocessing - Data Validation
The input of this pipeline is no doubt images. Since our team is building a data pipeline for a commercialized security system, its reasonable to assume those images are coming from different cameras of different properties. As a result, we should not treat all of them the same way for the whole process. How those images are generated and collected is out of the boundary of our pipeline.

Before we do object detection, we need to preprocess our inputs to ensure that the downstream processing happens only on qualified inputs rather than wasting time on garbage. We need some rules for input validation, and the same set of rules applies to all the inputs regardless of their sources. When we generate the alert, we want to alert a specific user or group of users, so it’s important that we have an existing tag recording when and where (in terms of a user or device) is the image coming from at the beginning. In other words, we need at least a user id and a timestamp to indicate the location and time of each image, and our team decided the tag be in the format of "id_timesatmp.jpg" as image names, Specifically, we will use [UNIX timestamp](https://en.wikipedia.org/wiki/Unix_time) and the time zone is set to be UTC. If you want to replicate our system, you can define your own tag rules, and tags can actually in many other formats, which is really flexible. For example, you can have a separate text file for each image, or embed tags in the image and extract them later. However, you should always pay close attention to the track of time zone, especially if the system is planned to work on multilocation. Besides, we expect our inputs to be colored. We might also add rules on resolution if we have time, because poor resolution could cause trouble in detection. For this part, you can also enforce things like quality of image or size of image based on your model design. The detection model we will use here is quite flexible on size and format, so we are not going to restrict that. 

If officially put into business, we will have convention contract based on those rules, so that all images sent to this system for process are supposed to be in this format. But if you are processing from certain data sources which already have had their data conventions, you may want to build your pipeline according to their policy. Also, we will document the dataset we trained our model on to let users know objects they can expect to be detected.

Each image after validation process will be categorized either valid or invalid. In our case, we will discard all the invalid images without any further processing, like doing some enhancement, but we do plan to keep them seperately in case there are problems requiring us to go back to those images to debug. For example, if we do not receive any threats report for a device over an unusually long time, we need those data to figure out if the problem lies with the device or our process settings.
### Object Detection
With input standardized, the next step is to feed valid images into the object detection model we described in the previous writeup. Output from here is the class detected, which can be in the form of image or text representations, such as a JSON file. To carry through the identity of the image, we will name the JSON file in the "id_timesatmp.jpg". The reason we have a JSON file to record the detected classes and corresponding probabilities in addition to marks on images is that it’s easier to automate around that text representation than images with box.
### Postprocessing - Threat Detection
After the object detection, we need one more detection process to determine the threat level, which we will call it threat detector. In our design, for this step we will process by ID. That is to say we will bucket everything from each ID together, and process things under the same ID (folder) the same way through a loop. As you know, the definition of threats divers among users, so processing by ID is the most efficient way. The detector will take in the JSON files outputted from the last step, read in corresponding threats rules, a list of dangerous classes required by the specific user, and match objects against the rules. You do not have to document anything about classes that are not dangerous. Any detected thrests will be written back into the JSON file.


### Postprocessing - Alert Service
 
 
Locations corresponse to ids, create bar plot of this repo, almost same thing, id1,2 under each of this folders, coy over ones are threats and put the mover here, we can say just the counts of the threats, just a fiel of numer in it. Either way we can have a processing stpes here. Keep historical record, packyderm piece already provides this dataversioning for each of this repos, increametatlly processed already, wjat s new not new, based on increamental changes.complinace. gargabge collect over tiem to not waste space, we could just copy those time.jason over here, ones that are threats, because we are versioning, and analyzing what data is where, Pakerderm automatically deduplicate that for us. Not pernlyt to make that copy, is trose the same place under the hood. Automitally you are copying, your copy already exsitn, I’m not goin tomake another copy, just create a pointer. Another advantage is look hwt’s here rather than just look at counts.

 
One way is 
leteallt, we maywant generate a bar plot per day  per hour or notify the user this happen this time,

## Output
To get an image with object detected is not the end of our pipeline. The ultimate output should be a notification to the specific user as well as related deaperment when an threat is detected. We will realize this in email delivery vai API.
Notification, warning,a rlam , output. How to triger such. Have user id from tag, how to trigger, send an emailcompiuter where I check my email,like a user what comes out his end, images to the user. There is something inbwteen email sevice, text clal th police, some service, alert service,this company  build aroud this. Email delivery service via api
We got that service, if this service here, send some api call to the service, what aactually needs to comeout of here image anad users nmane in what form, api cal may be in json form, aleet clinet.imsge text. 
Actual users here, in our companywe also want to maintain an internal visiblibility aorung whee and how things are tragin g, how often they are ftrgiering and varies statistic. How might we do that. Got users taken care. How many tiems, which loctiaons are alerting the most.dashboead an adatabse where we load this. For our purpouse, we plot something, not a ful dashboard, all loctions bar plot, which one has the most,, partial visisbility. Some sort fo ploits. 
Version cllosetcs of daa, varies piece of data, input or ouptout or just intermietemetn. Organization of dat,
In some aspect, allsert a didfertn user when the first user has some threats. Abounch of images forma aboung of difiernt properoties.
Assume have a way of getting this to us, not wrroy about the magniesm. 
you could  Doing analytics with time, vry important. Timezone for compaies with golabl prensen, we can’t do nayhitng in this form. We put a rule is utc time.
Data maniupulation stage, colud just be in python, cloud be move things aroung, copy form there to ther ,we  can auto matea theta , evne t=with python, with some sricpt. Change their sheaf we can serch through files.we cando some ploting may be with python , may be with r. building blocks. 
Something should be documented if we choces it conventions.
Fi mach api call
