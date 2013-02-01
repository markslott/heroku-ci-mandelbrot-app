Heroku + IronWorker + Amazon S3  Compute Intensive Example using Fractals
==============

Quick intro
--------------


This is a demo that will put a compute intensive workload on Heroku.  This demo is using the IronWorker add-on to parallelize multiple computational workloads and then aggregate the results together.  The compute intensive workload is the computation of a Mandelbrot Set image.  If you don't know about the Mandelbrot Set or fractals in general, then look it up on Google…. the short of it is that the application will have to mathematically generate every single pixel in the image by computing against the fractal equation.  It takes time to do this, but the more horsepower you've got and the more parallelism you can take advantage of, the faster you get the result.

Once this is configured, you will be able to build out a sizable and quite detailed image of the Mandelbrot Set.  Feel free to experiment with different values in the code -- it is fun.

You will need an Amazon AWS account to run this demo, so sign up for one -- it is free up to a certain point.



Instructions on setting things up 
--------------

1.  Create a new Eclipse workspace

2. Import the MandelbrotDemo.zip archive file into the workspace (File -> Import -> Existing Projects into Workspace)

3. Fix the build path for the project.  The .jar files used are included in this zip file.  Just right click on the project, select Properties.  Go to the Java Build Path tab.  Click on Libraries.  From here, you should be able to fix the path to the .jar files so the app will compile.

4. Get an Amazon S3 account http://aws.amazon.com…. once registered you will have an access token and a secret -- you'll need these later.

Go ahead and log into the AWS console (https://console.aws.amazon.com/s3/) and create a bucket.  Remember the name of your bucket.  

5. Make a new directory… doesn't matter where. 

6. Create a new Heroku instance and add the Iron_Worker add-on:

(instructions for this step can also be found at https://devcenter.heroku.com/articles/iron_worker#get-started)

heroku addons:add iron_worker:development
gem install iron_worker_ng


-Once that is done, the following command:

heroku config --app <your heroku app name> | grep IRON 

-This will give you two values (IRON_WORKER_PROJECT_ID, and IRON_WORKER_TOKEN).  

-Copy the iron.json file to your new directory and add these values to the file.

7. Open the MandelbrotDemo project in Eclipse and open com.sfdc.msl.mandelbrot.AwsCredentials.properties

Enter your secret Amazon S3 information here.

8. Open the com.sfdc.msl.mandelbrot.Enqueue.java file

-Add your values from step 5 to these variables 

	private static final String IRON_WORKER_PROJECT_ID = "";
	private static final String IRON_WORKER_TOKEN = "";

-Change the following field to your S3 bucket name
	private static final String AMAZON_S3_BUCKETNAME = "";

-Save the file.

-At this point, your app is configured… Now it is time to deploy:



9. Right click on MandlebrotDemo and select RunAs -> Run Configurations
Right click on Java Application and select New
Change the Name of the configuration to "Worker"
Press the Search button on the Main Class field and select Mandelbrot
Click the Apply button
Click Close

10. Export the project as a runnable JAR file:

File -> Export -> Runnable Jar File

-Select "Worker" as the run configuration
-Click the Browse button and navigate to your new directory.  Give the export the name mandelbrot.jar

11. Copy the mandelbrot.worker file into your new directory.

12. Deploy the jar to Heroku:

-From a command prompt in your new directory:
iron_worker upload mandelbrot

-This will start the upload process.  It will take a minute.  Once complete, you are ready to run.


13.  Right click on the MandelbrotDemo project in Eclipse and select RunAs -> Run Configurations.
Right click Java Application and select New

-Press the search button next to Main Class and select Enqueue.
-Give the configuration a name of Enqueue.

-Click Apply.

-Click the Run button

-You will see the output of the Job in the Java Console in Eclipse.  It takes about 1 minute using the default config to produce results.  Once it is done, download your mandelbrot_mosaic.png file from the S3 console… you just ran a compute intensive workload and are now seeing the results.

Your output should look like the image included in this zip file.

