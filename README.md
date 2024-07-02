 

# Automating User and Group Management with Bash
In a fast-paced development environment, managing users and groups efficiently is crucial. In this article, I will walk you through a bash script that automates user and group creation, sets up home directories, generates random passwords, and logs all actions. This solution is particularly useful for SysOps engineers dealing with onboarding new developers.
Prerequisites
Before we dive into the script, ensure you have the necessary permissions to create users and groups on your system. Additionally, you need openssl installed for generating random passwords.


The Script: create_users.sh
Here's the complete script with explanations:
Step 1: Initial Setup

#!/bin/bash

# Check if the input file is provided
if [ -z "$1" ]; then
  echo "Usage: $0 <name-of-text-file>"
  exit 1
fi

INPUT_FILE="$1"
LOG_FILE="/var/log/user_management.log"
PASSWORD_FILE="/var/secure/user_passwords.txt"

# Create log and password files if they do not exist
touch "$LOG_FILE"
mkdir -p "$(dirname "$PASSWORD_FILE")"
touch "$PASSWORD_FILE"
chmod 600 "$PASSWORD_FILE"
We start by checking if the input file is provided. The script logs all actions to /var/log/user_management.log and stores passwords securely in /var/secure/user_passwords.txt.
4:13
Step 2: Logging and Password Generation Functions
# Function to log actions
log_action() {
  echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" >> "$LOG_FILE"
}

# Function to generate random password
generate_password() {
  echo "$(openssl rand -base64 12)"
}
These functions help in logging actions and generating random passwords.
Step 3: Reading and Processing the Input File
bash

# Read the input file and process each line
while IFS=';' read -r username groups; do
  # Remove whitespace
  username=$(echo "$username" | xargs)
  groups=$(echo "$groups" | xargs)
  
  # Create user and personal group
  if id "$username" &>/dev/null; then
    log_action "User $username already exists."
  else
    useradd -m "$username"
    log_action "User $username created."
  fi

  # Create personal group
  if getent group "$username" &>/dev/null; then
    log_action "Group $username already exists."
  else
    groupadd "$username"
    usermod -aG "$username" "$username"
    log_action "Group $username created."
  fi

  # Add user to additional groups
  IFS=',' read -r -a group_array <<< "$groups"
  for group in "${group_array[@]}"; do
    group=$(echo "$group" | xargs)
    if getent group "$group" &>/dev/null; then
      usermod -aG "$group" "$username"
      log_action "Added $username to group $group."
    else
      groupadd "$group"
      usermod -aG "$group" "$username"
      log_action "Group $group created and added $username to it."
    fi
  done

  # Set up home directory permissions
  chmod 700 "/home/$username"
  chown "$username:$username" "/home/$username"
  log_action "Set up home directory for $username with appropriate permissions."

  # Generate random password and store it securely
  password=$(generate_password)
  echo "$username,$password" >> "$PASSWORD_FILE"
  echo "$username:$password" | chpasswd
  log_action "Password for $username generated and stored securely."

done < "$INPUT_FILE"

log_action "User creation script completed."



The script reads each line from the input file, creates users and groups, sets up home directories, generates passwords, and logs all actions.

# Conclusion
This script simplifies the process of managing users and groups, ensuring security and efficiency. By automating these tasks, SysOps engineers can focus on more critical aspects of their work.
For more insights into the HNG Internship, visit https://hng.tech/internship and https://hng.tech/hire. The HNG Internship provides an excellent platform for learning and growing in tech. (edited) 
hng.techhng.tech
HNG Internship | HNG
Learn from the best in the game and become world-class!
hng.techhng.tech
HNG
Start your journey to becoming a world-class developer today!










































