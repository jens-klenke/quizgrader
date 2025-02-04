# Documentation for working on the package


## Package structure/use 

There are two Shiny apps which provide user interfaces for the instructor to create and manage the quizzes and for the students to submit quizzes. Those are in the `/inst/apps` folder. All instructor-facing tasks can be done without the UI through a number of functions described below. Those are in fact called by the UI. The vignettes provide a lot more detail on package use.


## Package organization


### Folder structure

* `/auxiliary` contains folders with related resources that are not directly part of the package but are useful (to the package authors) for maintenance/development/tracking/etc. of the package
* `/docs` contains the package website created by the pkgdown package. Rebuild with pkgdown::build_site(). Don't edit manually.
* `/inst` contains several subfolders, further described below. 
* `/man` contains the documentation for all public functions, automatically generated by roxygen. Do not edit.
* `/Meta` contains vignette information and is auto-created by devtools when package is built. Ignore.
* `/pkgdown` contains extra files for styling of pkgdown created website. Edit to change layout of package website.
* `/R` contains the main R functions, i.e. the simulators and various helper functions. See more details below.
* `/tests` contains code/unit tests, done with the testthat package. Add tests as new functionality is added.
* `/vignettes` contains the vignette - this is copied to /inst/doc during package building. edits should be done to the file in the /vignettes folder, not the /inst/doc folder.



### Materials inside the /inst folder
The `/inst` folder contains several subfolders. 

* `/apps` sub-folder contains the different shiny apps which are part of the package. There are two apps for this package. The `quizmanager_app.R` app is the one used by instructors to make and manage the quizzes. It is started by typing `quizmanager()` into the console. The quizgrader app (called `app.R`) is deployed to a Shiny server and used by students for submission. The CSS file is for styling of both apps (and as such needs to be deployed together with app.R).

* `/templates` sub-folder contains the Excel templates for the class list and quizzes.


### Functions/files in the R folder

The following functions provide functionality for either quizmanager (marked with QM) or quizgrader (QG). 
All QM functions can be called directly without using the UI. (The GQ related functions could probably too, but the grading portion is not meant to be used without the UI)

* `quizgrader.R` only contains general information so a user can do help('quizgrader')
* `quizmanager.R` script starts the `quizmanager_app.R` app.


Functions for QM:
* `analyze_` - functions run by the different analyze buttons, each analyzing submissions in a different way.
* `check_quiz` - makes sure complete quizzes are properly formatted and filled.
* `check_courselocation` - checks if a provided/chosen location is the main directory of a quizgrader quiz setup
* `check_studentlist` - makes sure that student list is properly formatted and filled.
* `create_course` - starts a new course, creates starting folders and files
* `create_serverpackage` - makes a file/folder bundle for either initial deployment or update that needs to be copied to server 
* `create_studentquizzes` - takes the completed quizzes, processes them to produce quizzes for students, also places them into a zip file
* `summarize_course` - creates summary information about course.

Functions for QG:
* `check_metadata.R` - checks that user input (user ID and password) match information in the student list.
* `check_submission.R` - checks a submitted quiz for proper formatting before attempting to grade it.
* `grade_quiz.R` - grades the submitted quiz.




## Information for package development

### To work on package (through RStudio): 
* Clone package.
* Load project in RStudio. Edit files as needed.
* Use devtools/roxygen to build and document the package. Either use RStudio's functionality (or command line) to test/build packages using devtools. The package follows Hadley's recommendations and workflow described here: https://r-pkgs.org/ 
* Optionally, use RStudio tie-in with github to sync project to github (the 'git' tab). Alternatively, use your favorite git client.
* Rtools needs to be installed (on Windows).

### Dependency packages 
All libraries/packages needed to allow the package to run should be loaded via the DESCRIPTION file and not in separate R files. See that file for dependencies.

Additional packages are needed for development (but not use) of the package. Those are listed in the suggests section of the DESCRIPTION file. 

### Github action integration
To enable continuous checking and other GitHub actions, use the 'usethis' package as follows:
* use_github_action_check_release()


### Package logo
According to pkgdown, logo should be included as described here:
https://pkgdown.r-lib.org/reference/build_home.html#package-logo


### To update R documentation, vignettes and website
* Edit documentation inside R functions, build documentation with More/Document or devtools::document()
* Edit vignette inside the /vignettes folder, run devtools::build_vignettes()
* Edit Readme.Rmd and other package website related documents, run pkgdown::build_site()


### To build the package
* in RStudio, use the functions in the 'build' tab to test and build the package.
* Run clean and rebuild, then build and reload using menu, or devtools::load_all()
* Run package check, fix any errors 


### To-do for CRAN release  
* "by hand" edit the DESCRIPTION file to make sure it's up to date
* update news.md and cran-comments.md
* run processing-script to Re-build documentation, vignettes and copy latest versions of simulator functions - see above
* Run check and make sure no problems occur
* Sync everything to github
* Run devtools::check_rhub(), devtools::check_win_release() and devtools::check_win_devel()
* Do a test run of devtools::release() - fix any remaining issues, then re-run and release

### Trouble-shooting
* If you get the 'can't find qpdf error' during build, try this (on windows): https://stackoverflow.com/questions/41570633/how-to-build-qpdf-on-windows
