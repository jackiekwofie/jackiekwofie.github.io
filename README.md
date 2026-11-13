
# Automating User and Group Management with Bash

In a fast-paced development environment, managing users and groups efficiently is crucial. In this article, I will walk you through a bash script that automates user and group creation, sets up home directories, generates random passwords, and logs all actions. This solution is particularly useful for SysOps engineers dealing with onboarding new developers.

## Prerequisites

Before we dive into the script, ensure you have the necessary permissions to create users and groups on your system. Additionally, you need openssl installed for generating random passwords.

## The Script: create_users.sh

Here's the complete script with explanations:

### Step 1: Initial Setup

```bash
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






























