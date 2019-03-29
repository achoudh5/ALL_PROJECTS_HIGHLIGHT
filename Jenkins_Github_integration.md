Git clone <repo to be cloned>
Inside the submodule directory:-
git submodule update --init --recursive
This is to make sure all the files inside the submodule directory gets cloned as well.

You can do it directly as well while cloning by using:-
git clone --recurse-submodules <repo to be cloned>

**Pre-Requisites**:-
Files required at the repository where the test & deploy scripts would be running and **not** your remote repository,

*tox.ini*
*setup.py*
*tests* (which are to be tested)

Why do we need the files mentioned above ?
tox is a generic virtualenv management and test command line tool you can use to run automated tests against four different versions of python on different platforms:-

**Example file** of tox.ini :-
[tox]
envlist = py27

**#Checking that package installs correctly with different Python versions and interpreters**
[testenv]
install_command = pip install . {packages}
depts = coverage
setenv = PYTHONPATH={toxinidir}
commands =
        coverage erase
        coverage run -m unittest discover
       

**#Coverage tells you what parts of your code are actually getting executed by the test suite. It can't guarantee that you're testing that code properly, but it'll tell you if there's code that isn't being tested at all**

***The exit status at the end of the test will tell us if there was a successful or failure test.***
deps=
        mock
        discover
        Coverage

**The discover allows to discover and run unit tests and we can easily integrate it in a tox **

**#Running your tests in each of the environments, configuring your test tool of choice**
   

  b) setup.py
It  is a python file, which usually tells you that the module/package you are about to install has been packaged and distributed with Distutils
https://stackoverflow.com/a/39811884/8071222

**Step by step guide**:- This is a step by step guide for deploying the package to pypi server across all sites (Antwerp, Mountain View, Raleigh).

At **Github enterprise** :- These are the things which the **owner of the repo**  has to make sure are configured correctly before they test the build on Jenkins for any repository.

Make sure you can view the settings tab on the repository.

Inside the settings:-

Under *Collaborators & Teams* subsection

Add **Bot** and **Jenkins** as the collaborator and make sure to give them the **write** access. 
*Note*: If you don’t give the write access then the status checks would get updated and you would see that even after the success of the job build they are still stuck in running state.
	      
      b. Under **Branches** subsection
		   i. 	Select the Default branch as **master**, since all the PRs would be           
                                    against this branch (here)
		
                          ii.	Select the Protected branch as **master** and click on the edit option
                                    
                                   		a. Check protect this branch , require status check to pass before                                                
                                                    Merging, require branches to be up to date before merging.
		
                          iii.      Click on **Save Changes**
     
	      c.  Under **Webhooks** subsection
		  i.       Add a webhook http://mvdcjenkins.us.alcatel-lucent.com:8080/ghprbhook/
		
                         ii.      Click on let me select individual events and **check** the following items:-
		
                                  		a.  Issue Comment
				b.  Pull Request
				c.  Push
				d.  status
			            e.  Active
		Update the webhook.
     
                      iii.       Click on **Services** subsection

		Add this service http://mvdcjenkins.us.alcatel-lucent.com:8080/github-webhook/
		Click on update service

      3. Make sure that the **submodule infra/python-ci** is present on the repository, to make     .                   
          any of the scripts such as clean, deploy and test to work.
           
At **Jenkins** these are the things which the person with the **write permissions** has to make sure are configured correctly before they test the build for any repository.

Generally, the **write permissions** is with the Nuage Infra team. So, if the person is an infra- team member hen follow the steps down below:-	

After you have started the project for the repo, go to the configure tab on the Jenkins page.
 1.  Under the general section make sure these options are checked:-
	
i.  GitHub project -- add the https link of the repo here. 
           
           ii. Execute concurrent builds if necessary
           
           iii.  Restrict where this project can be run
        
 2..    Under the source code management:-
                            
 i. Add the repository link git@github.mv.usa.alcatel.com:pygash/xx.git under git.
	      
            ii. Under the Advance section of the repositories add 

a. Name- origin
                         
            b.refspec-**+refs/heads/*:refs/remotes/origin/*+refs/pull/*:refs/remotes/origin/pr/***
           
           iii. Branches to build:-

a.  Keep the branch specifier as **${sha1}**
		
          Iv.  Click on **Additional behaviors** , this is for the working of **submodules**.

	   Check these options under that:-
		
                      a.   Recursively update submodules
           		b.    Use credentials from default remote of parent repository
  


3.  Under **Build Triggers**, check **GitHub Pull Request Builder** with these options:-
 
	i.  Allow members of whitelisted organizations as admins

           ii.  Build every pull request automatically without asking (Dangerous!).  

          iii. Check the **GitHub hook trigger for GITScm polling** option.

4. Under **Build Environment** make sure these options are checked:-
	
i.  Add timestamps to the Console Output
	 
	ii. Set Build Name
	
Set build name before build starts

                 b.   Set build name after build ends
         iii. Set GitHub commit status with custom context and message (Must configure      

             upstream job using GHPRB trigger)

         iv. Under *commit status build* add success, failure and error options.

5. Under **Build** give the path to the script to be executed for *clean, test and deploy*.

6. Under **Post-build Actions** 

i. Click on add and select *Set GitHub Commit Status(universal)* 


If the person is a non-infra team member and owns the repo then he needs to give the following details to the person with the **write permissions** on the Jenkins job:-

Name of the Repository for which the configuration has to be done for the build job.
Whether any submodule is being used or will be used, otherwise build job might get fail.


(c)  *Tests*
     
If there are no tests then the ./python-ci/test will always be successful and it will display 
** python: commands succeeded
  congratulations :)**
And it will always be a success check in the github, so the PR would raise no concern evenif  actually it might throw an error if the tests were run using the sub-module test script. 
	
** Sub-Module **

# Private_Content
