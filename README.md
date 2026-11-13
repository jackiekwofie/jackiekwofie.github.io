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
```

The script reads each line from the input file, creates users and groups, sets up home directories, generates passwords, and logs all actions.

## Conclusion

This script simplifies the process of managing users and groups, ensuring security and efficiency. By automating these tasks, SysOps engineers can focus on more critical aspects of their work.

For more insights into the HNG Internship, visit [https://hng.tech/internship](https://hng.tech/internship) and [https://hng.tech/hire](https://hng.tech/hire). The HNG Internship provides an excellent platform for learning and growing in tech.













