## AUX PROJECT 1: SHELL SCRIPTING
Write a shell script to onboarding 20 users. The script must meet the following conditions:

- Create the project folder called Shell

- Create a csv file named names.csv inside the Shell directory

- The script you created should read the CSV file, create each user on the server, and add to an existing group called developers (You will need to manually create this group ahead).

- Ensure that your script will first check for the existence of the user on the system, before it will attempt to create that it.

- Ensure that the user that is being created also has a default home folder

- Ensure that each user has a .ssh folder within its HOME folder. If it does not exist, then create it.

- For each user’s SSH configuration, create an authorized_keys file and add ensxure it has the public key of your current user.



The Script implementation is shown on the screenshot below:

