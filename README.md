# Designing Pipeline
## Building up a Data Pipeline
A way to detect objects in data, which is couple of building blocks we are doing. Premium post processing , finishing design of our core pipeline, we have object detetion piece and how to stroe the dta, that’s not the full picture. Automated pipeline, wwe put image on the top and value comes out the other side. Right now we just talks abaout how to detectbjectsin miages without other kind of pieces. 
Where dploy, amazon.tensofrlow came of of goole, kubernet came out of google, certain conviouesoucs
Work toghethte ron desiging. Detect and come up with a design.
What this meng for deisinging this.
We have Some input dat, one of the data repository, we gong create this version collectiongs of data,s3, processing step, like lsat week, we going to detect objectcs in those images, typthon sript, data output form that, whatervver,we gongna output.
A pece we going to put togethere todea,
Maybe we have multiple input/some post processing on that, a couple of things , we cominigthose agoian in the end.
Images comes in, a seriese of action would haappned aftr words,
All the data will be back by s3, the processing happening onamazon ec2 instances, where actually deploy things and where they run. 

To build a data pipeline, we usually need to consider three parts. THe first is input, which in our case is a Pachyderm Data Repository.
Then we need a processing step. For example, a python script. After processing, we get data output from processing.
## Input
The input of this pipeline is no doubt images. Since our team is building a data pipeline for a commemcerilized security system, its reasonabele assume those images are coming from difrent camers of diferent proepreties. As a result, we should not treat all of them the same way. 

## Output
Notification, warning,a rlam , output. How to triger such. Have user id from tag, how to trigger, send an emailcompiuter where I check my email,like a user what comes out his end, images to the user. There is something inbwteen email sevice, text clal th police, some service, alert service,this company  build aroud this. Email delivery service via api
We got that service, if this service here, send some api call to the service, what aactually needs to comeout of here image anad users nmane in what form, api cal may be in json form, aleet clinet.imsge text. 
Actual users here, in our companywe also want to maintain an internal visiblibility aorung whee and how things are tragin g, how often they are ftrgiering and varies statistic. How might we do that. Got users taken care. How many tiems, which loctiaons are alerting the most.dashboead an adatabse where we load this. For our purpouse, we plot something, not a ful dashboard, all loctions bar plot, which one has the most,, partial visisbility. Some sort fo ploits. 
Version cllosetcs of daa, varies piece of data, input or ouptout or just intermietemetn. Organization of dat,
In some aspect, allsert a didfertn user when the first user has some threats. Abounch of images forma aboung of difiernt properoties.
Assume have a way of getting this to us, not wrroy about the magniesm. 

## Building blocks
Object detection piecie, pythonand tendorlaow to do object detection, takein iimpages and out put amunber of thing, another picuter lst week. The actual class name , tags asociaited with , count of detected things. For example., this is basjiclaly the same model, so Ik like a ccpivtue, instead of get that pinctuer sm output some text. Not limit to only putput imge.
Data maniupulation stage, colud just be in python, cloud be move things aroung, copy form there to ther ,we  can auto matea theta , evne t=with python, with some sricpt. Change their sheaf we can serch through files.we cando some ploting may be with python , may be with r. building blocks. 
Something should be documented if we choces it conventions.
Tag this things some way for the jio to process things some way. Not necesraity enforced here, but conventional rule should be followed for the image to be proceesed in a good way. 
Somweshwet out here in the midel, we want to detet objects, berfore we do that , we might wnt ato do some preporccsing, after we do that thm, we may awnt some postprocessing, so lets start we peobabailtiy should aat the beginning, 

### Conventions & Rules
Before we do object detection, we need to preprocess our inputs to ensure that the downstream processing happens only on qualified inputsrather than wasting time on grabages. would like to set up some conventions. 
I GOT ABOUN OF IMAGES comsing in, something.jpg, jpgk, do I NEED TO DO ANY THING BEFORE I DO DETECTION. Someone have servie somewhere we=ill feed you images, and you gonge ake this to output. Most of tiem inthose senaiors, those teams set up what they call ocntanct or policeyse, if we setup this pipiele, it would be perfectly fine for us to say, if you wna tus to do thism, you need to do this image rules. Or it cloud be the other way around, if this is built first, it could be that they have a conteoct says if you wan tot consume the imgaes, form our services, this are what it gona be formatte. Not necessary a person in between, more of an explicit contract about how you going to do the mor how you should expect to get them, isn’t like a processing stuff.
If our service si failing to detect a object we are interesting in, if we expend to the astrulia, we didn’t expect, we fail to detect cagorros, that’s really aprobelm in our end, our model is not performing as it shou;ld, retrained the model aon aosmedataset includes cagoon, and update our pipeline, that wouldn’t nedd knowledgea form anyone else. But it would definitely be something we like a teamwant to document, like this is the data we trained on, if there is any label outside that, we don’t expect to eb able to detect. We can detect people and car and foges ,a cats, kined of the ocmon things.

We also need some rules for input validation, and the same set of rules appies to all the inputs regradless of thier sources. We expect our inputs to be colored. We might alos added rules on resolution if we have time, beacuse poor resolution could cause trouble in detection. For this part, you can also enforce things like quality of image, size of image or resolution of image based on your model design. TThe detection model we will use here is quite flexible on size and format, so we are not going to restrc that. 
  
 loop questions of imags into a validation,
 if I got imges coming in to reporsitory, images called images,  (based on the requiremtn of the user, if it placed on the public area, people scoming in andout, policy as when I alert, not about their content)we could have some rule for this is ok for the building not ok, after detection. Lets call this validation of poerperoty of images, output of this validation ias going to be, validated and standized image.
Invalid images, fro our purpose, we thorugh those in their, escceatinally disregard them, but if someone says thers is a porblem or we notice that a bar dropped, we  can go bakn there to debug,(tensofrltolw to fil the image)enhance images, void discard, equipment is bad.
Going to be implemented as a Data repository ,actually in the actual pipieple, output from each step will be versioned together,  the reason is you want have a clear chian of what generated what. Bu there ;ets say we have a directory for valid and invlaid imges, performing chaekcs on the images to ensure they are in right format , good resolution.
When we generate the arlet, we want to alert a specific user, goruip of user. Emnsure we have a good tag, exsting tag. What user or device is coming from, some tag associated with. How dowe want tag those imgaes, how should we. User and timestamp. Some indication of user or deice where it came form, we laso want time stamp, leteallt, we maywant generate a bar plot per day  per hour or notify the user this happen this time, so for imgse we have id-timesatmp.jpg, where identify the user/deive, unixtimestamp, number of secons past, sinle number give s indication of a time, we assuming, our convention /rule is saying whatever the service gives us images, has to give us imges inthis form, they may have a diceve to genretae, you could have an images and a text file coming in, or embed them in the imge somehow and extract them, the name of the file. Doing analytics with time, vry important. Timezone for compaies with golabl prensen, we can’t do nayhitng in this form. We put a rule is utc time. At lseat the id and timestamp, oculd do resolution. 
Have imgaes we can use. Detectin gimage, we have this step kind of look through last week. Uses python and tensorflow, feed the valided images to that object detection, no need to process this, because we know they are invlaid ,we can’t use them. Ouptput from here is class we detected, output some not image file, such as Json, folder, classe, in here is going to be Jason files, how do we know whithc jaosn,we name this  id-time, carry th=rough, not lose context, json should lnclude this things.I have the class in json format for each iamge, what objecs in ther and correspongbing probabilityies. List of dandeous classes and not dangerous. Output json with classes. How we go nona represent those classification in data. Automated.whtere to alret sb or not.  It’s easy to automated around that text reprensentation than iamge with box. 
We gonan have some detection process steps determing the threat level, threat detector. Taking in thse json files, python script, how to deffirentiate. You colud combaine them, reason might not want to the more functnality you lump into one piees, we might want to do some thing diferent for each user, and we want to do things at the same time fro multiple sifternt users, if we process imges form all of them, we don’t want do in leianre. Prellerl, perlelizing one of this steps. Each one if we s;lit up, first of all, object detctionhas a spesifc trials of tool to do that, which might be difrent form what I want to use here or there, or later on down the line, by split up m it automatically give me the freedom to useless tools  for a particular step. R java, also this dift stpes hav edifer implication for scaling, in other words, object detection might a bit lnger than validation, so if I SPLIVT HTINS UP, I CAN USE DIFERENT TOLLS FOR DIFRENT STEPS AND I CAN SCALE THEM DIVETNERTLY, I can dploy seven worker s to do validation and a huenrd works to do object detection, ont to do detection, scale each one independently, based on if it’s a bottlemnack or not, don’t get the visibility into the work flow if I would, blackbox to be, not know which image wre validated, or what calss wree detected and disgraded, unless login entyihg out, then I have sift through all of that and build log analyzer. Either way you have to separate things, it’s just whtere youwna tto do it in a log or here. 
One way is process by id, can process all thing stogether have the same id, in the same way, how data organized, want toprocess users independtly, buket everything from each id together, group things together, if group by id together. I can eeisly process each group together, I could earch thrugh the json and loopover the json. After object detection, option 1 , look thouth al thme, look what id I have and process by , or process by group, if group, easy can just output by file name to the folder, don’t have to sech through anythong, know goes together. Rules input to threst detector, indentify threats and non threats, make things easity, don’t have to write everything, just need to write down threarts. Per user, could have by ID, threats are the same here. Match or no match. Fi mach api call
Locations corresponse to ids, create bar plot of this repo, almost same thing, id1,2 under each of this folders, coy over ones are threats and put the mover here, we can say just the counts of the threats, just a fiel of numer in it. Either way we can have a processing stpes here. Keep historical record, packyderm piece already provides this dataversioning for each of this repos, increametatlly processed already, wjat s new not new, based on increamental changes.complinace. gargabge collect over tiem to not waste space, we could just copy those time.jason over here, ones that are threats, because we are versioning, and analyzing what data is where, Pakerderm automatically deduplicate that for us. Not pernlyt to make that copy, is trose the same place under the hood. Automitally you are copying, your copy already exsitn, I’m not goin tomake another copy, just create a pointer. Another advantage is look hwt’s here rather than just look at counts.
