# CCBextract

The following scripts were created to simplify migration of data off of Community Church Building (CCB) to Planning Center Online (PCO People).

First a bit of history…When we migrated from CCB to PCO in the Fall of 2016, the process of extracting information out of CCB was [quite painful](http://www.bottomshelvz.com/2017/04/why-we-migrated-from-ccb-to-pco/), while the import process into PCO was simple and quick. Even though our migration is complete, I thought it might be of value to the PCO to write a script or two to simplify this CCB export process, and possibly do a little bit of prep for PCO import as well.

## Why Ruby Scripts?
The method to export the information out of CCB to could taken several different forms. I chose to use simple composite Ruby scripts for a couple of reasons:
1) A lot of the PCO community, and even some of the PCO team themselves, seem to like Ruby

2) Ruby is pretty much available on any platform and is simple to install. If you are on Mac OS (like a lot of the church church seems to be these days), then you already have Ruby natively installed and ready to go. I you are are Windows, its simple to install ruby (links are below).

3) This was created as a downloadable script, rather than a web service or hosted app, mainly to eliminate risk to your data. The app requires a CCB API Key to be generated and used that will allow access to all of your data. If I had done this on a hosted platform, no matter how much we promised, there's always a perceived risk that somehow we might be keeping or not securing your API keys, putting your data in CCB at risk. Running this as a local script on your machine, where you never share the keys with us, mitigates this risk.

## Any other risks?
Running these scripts should be low risk, as we have ONLY used read api calls, which should never change or delete any information.

## Getting Ready
There are a few steps to complete to get ready to run the script(s)
1. **CCB API Info**
    1. Logon to CCB using an admin level account, click on the preferences gear in the upper right corner, and then click on API. If you cannot access this menu or the API option, your are likely not logging in with an admin account.

    2. Copy Your API URL, saving it for insertion into the script. This will be specific to your account, and needs to be set properly.

    3. Next, if you dont already have an API User created, click on Add a new API User on the right. Enter the name/username/password fields, using a service account username like ccb_api_user or similar.. the Org contact info boxes should be optional. Click Save to save this API user info, and save it with the API URL you captured in the step above.
2. **Checking / Installing Ruby**
    1. If you are running on Mac OS, simply open a terminal window — click on the Magnifying Glass on the task bar, type terminal, then return. You will be greeted with a plain old command line interface, which should be in your user folder (/users/<Your Username>). In the terminal window, type
```ruby -v```
then enter. You should get some info about the Ruby version installed, indicating things are working.
    2. If you are running on Windows, go to https://rubyinstaller.org/, download and install Ruby. Once installed you should be able to do the same test from within a Command window (cmd), verifying that Ruby is properly installed.

## ccb-people-extraction.rb
The first script that you're going to want to run is the CCB people extraction script (ccb-people-extraction.rb), which will extract all of the people data out of CCB and provide you with several outputs:
* A CCB Export CSV file, containing all available information that came out of CCB, generally unfiltered or untranslated.
* A PCO import CSV, which is much of the same data, but formatted to align with the prescribed headers for PCO import. This file should be (essentially) ready for PCO import, and most of the headers will be recognized by the PCO import tool, and automatically
* CCB supports images per person and per family and during the extraction each person record might have an image field (as a URL), so the script downloads of each of these files, if available, and places them in one of two image folders (person and family)

### How to use
1. **The first thing** you're going to want to do is some field mappings. CCB and PCO have some minor differences in certain field values, such as membership status, marital status, name suffixes, etc. For the most part, we've left these values alone. But, you will want to make sure that any of these values you are using in CCB is also in PCO. This is more true is your church has modified the CCB customizable field values.
    1. Log into CCB, Preferences (gear on the upper right), and select Customizable Fields
    2. Click on Member Type to get the list of Member Types available in CCB.
    3. Now log into PCO People, click on the People Tab, then preferences (gear on the upper right), and then click Customize Fields
    4. By default you should be on the Personal tab, and you should be Membership Status as one of the fields listed. If not, click on the other tabs to find Membership Status.
    5. Click on the Dropdown under Membership Status, and review the available values. If you need to add a value to match up to CCB values, click on the Pencil to the right of Membership Status to add or edit these values.
    6. You will want to review and update PCO for all of the following fields:
        1. Marital Status
        2. Name Prefix
        3. Name Suffix
    7. If your CCB instance was setup for multicampus, you will want to create a field in PCO to place this value to be imported. This can be anything you like. For example, we created a Tab called Campus, then a field called Campus to hold this info.  
2. **Download the script** (from Github) and open a terminal/command window in the same folder as the script. For example, if you downloaded the script into your Downloads folder, you will want to cd /users/<username>/Downloads.
3. **Edit the script**
   1. Open the script with a text editor of your choice
   2. Edit the username, password and ccb_url fields with the values you created and captured in the CCB API Info section above. These values are strings, and will need to be single or double quoted. Save the file, and close the text editor
4. **Test the Script**
    1. Using the same command or terminal window, enter the following command
        `ruby ccb-people-extract.rb test`
This will execute a simple test to ensure that the keys are accepted by CCB and connectivity is working correctly.
    2. If everything is working correctly, you should get a message indicating “Test successful”
5. **Now for the full extraction!**
    1. Again, with the same terminal window open, run the following command
        `ruby ccb-people-extract.rb`
    2. The script will take several minutes to complete the extraction, and begin to download the images, if any.
    3. Once complete, the script will give you a final count of the People record count it extracted, as well as the number of person and family images download. The script will also detect if campus names were in the extracted info, and make some recommendations for the import.
    4. The output of the script will be placed into an output subfolder in the folder you ran it:
        1. You will find  `ccbextract_<date>.csv` and `pcoimport_<date>.csv` files in the output subfolder. Both are date and time stamped, so if you run the script again, you will have the new and older copies of each.
        2. Person images downloaded will be in the `output/ccbextract_images` subfolder, and Family images will be in the `output/ccbextract_family_images` subfolder.

Please let me know any issues you have running this — while not perfect, the goal was to make the exit process from CCB less painful than it was for us!