# Designing Pipeline
We've discussed the setup of data storage and the base object detection model as separate parts. Generally, we will use s3 for storage, and then process the object detection on images using python scripts with Tensorflow to get some output. This writeup introduces the design of our core pipeline, in which we connect every piece we have to form an automated process that will get the images, process the images, and eventually deliver necessary alarm when threats are detected.
## Building up a Data Pipeline
To build a data pipeline, we usually need to consider three parts, which are input, middle processing steps, and output.
## Input
The input of this pipeline is no doubt images. Since our team is building a data pipeline for a commercialized security system, its reasonable to assume those images are coming from different cameras of different properties. As a result, we should not treat all of them the same way for the whole process. How those images are generated and collected is out of the boundary of our pipeline.
## Building Blocks
### Preprocessing
Before we do object detection, we need to preprocess our inputs to ensure that the downstream processing happens only on qualified inputs rather than wasting time on garbage. We need some rules for input validation, and the same set of rules applies to all the inputs regardless of their sources. When we generate the alert, we want to alert a specific user or group of users, so it’s important that we have an existing tag recording when and where (in terms of a user or device) is the image coming from at the beginning. In other words, we need at least a user id and a timestamp to indicate the location and time of each image, and our team decided the tag be in the format of "id_timesatmp.jpg" as image names, Specifically, we will use [UNIX timestamp](https://en.wikipedia.org/wiki/Unix_time) and the time zone is set to be UTC. If you want to replicate our system, you can define your own tag rules, and tags can actually in many other formats, which is really flexible. For example, you can have a separate text file for each image, or embed tags in the image and extract them later. However, you should always pay close attention to the track of time zone, especially if the system is planned to work on multilocation. Besides, we expect our inputs to be colored. We might also add rules on resolution if we have time, because poor resolution could cause trouble in detection. For this part, you can also enforce things like quality of image or size of image based on your model design. The detection model we will use here is quite flexible on size and format, so we are not going to restrict that. 

If officially put into business, we will have convention contract based on those rules, so that all images sent to this system for process are supposed to be in this format. But if you are processing from certain data sources which already have had their data conventions, you may want to build your pipeline according to their policy. Also, we will document the dataset we trained our model on to let users know objects they can expect to be detected.

Each image after validation process will be categorized either valid or invalid. In our case, we will discard all the invalid images without any further processing, like doing some enhancement, but we do plan to keep them seperately in case there are problems requiring us to go back to those images to debug. For example, if we do not receive any threats report for a device over an unusually long time, we need those data to figure out if the problem lies with the device or our process settings.
### Object Detection
Have imgaes we can use. Detectin gimage, we have this step kind of look through last week. Uses python and tensorflow, feed the valided images to that object detection, no need to process this, because we know they are invlaid ,we can’t use them. Ouptput from here is class we detected, output some not image file, such as Json, folder, classe, in here is going to be Jason files, how do we know whithc jaosn,we name this  id-time, carry th=rough, not lose context, json should lnclude this things.I have the class in json format for each iamge, what objecs in ther and correspongbing probabilityies. List of dandeous classes and not dangerous. Output json with classes. How we go nona represent those classification in data. Automated.whtere to alret sb or not.  It’s easy to automated around that text reprensentation than iamge with box. 


### Threat Detection

We gonan have some detection process steps determing the threat level, threat detector. Taking in thse json files, python script, how to deffirentiate. You colud combaine them, reason might not want to the more functnality you lump into one piees, we might want to do some thing diferent for each user, and we want to do things at the same time fro multiple sifternt users, if we process imges form all of them, we don’t want do in leianre. Prellerl, perlelizing one of this steps. Each one if we s;lit up, first of all, object detctionhas a spesifc trials of tool to do that, which might be difrent form what I want to use here or there, or later on down the line, by split up m it automatically give me the freedom to useless tools  for a particular step. R java, also this dift stpes hav edifer implication for scaling, in other words, object detection might a bit lnger than validation, so if I SPLIVT HTINS UP, I CAN USE DIFERENT TOLLS FOR DIFRENT STEPS AND I CAN SCALE THEM DIVETNERTLY, I can dploy seven worker s to do validation and a huenrd works to do object detection, ont to do detection, scale each one independently, based on if it’s a bottlemnack or not, don’t get the visibility into the work flow if I would, blackbox to be, not know which image wre validated, or what calss wree detected and disgraded, unless login entyihg out, then I have sift through all of that and build log analyzer. Either way you have to separate things, it’s just whtere youwna tto do it in a log or here. 
One way is process by id, can process all thing stogether have the same id, in the same way, how data organized, want toprocess users independtly, buket everything from each id together, group things together, if group by id together. I can eeisly process each group together, I could earch thrugh the json and loopover the json. After object detection, option 1 , look thouth al thme, look what id I have and process by , or process by group, if group, easy can just output by file name to the folder, don’t have to sech through anythong, know goes together. Rules input to threst detector, indentify threats and non threats, make things easity, don’t have to write everything, just need to write down threarts. Per user, could have by ID, threats are the same here. Match or no match. Fi mach api call
Locations corresponse to ids, create bar plot of this repo, almost same thing, id1,2 under each of this folders, coy over ones are threats and put the mover here, we can say just the counts of the threats, just a fiel of numer in it. Either way we can have a processing stpes here. Keep historical record, packyderm piece already provides this dataversioning for each of this repos, increametatlly processed already, wjat s new not new, based on increamental changes.complinace. gargabge collect over tiem to not waste space, we could just copy those time.jason over here, ones that are threats, because we are versioning, and analyzing what data is where, Pakerderm automatically deduplicate that for us. Not pernlyt to make that copy, is trose the same place under the hood. Automitally you are copying, your copy already exsitn, I’m not goin tomake another copy, just create a pointer. Another advantage is look hwt’s here rather than just look at counts.
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
