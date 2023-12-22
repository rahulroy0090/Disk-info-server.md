## New server script

```
import smtplib
from email.mime.text import MIMEText
import psutil
import paramiko

def get_server_info(server_ip, username, password):
    # SSH connection to the remote server
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    ssh.connect(server_ip, username=username, password=password)

    # Commands to fetch system information
    commands = [
        "hostname",
        "free -h",
        "df -h",
        "uptime",
        "mpstat -P ALL 1 1",
        "cat /proc/loadavg",
        "cat /sys/class/net/eth0/statistics/tx_bytes",
        "cat /sys/class/net/eth0/statistics/rx_bytes",
    ]

    system_info = f"Server IP: {server_ip}\n"

    for command in commands:
        stdin, stdout, stderr = ssh.exec_command(command)
        output = stdout.read().decode('utf-8')
        system_info += f"\n{command}:\n{output}\n{'='*30}"

    # Close the SSH connection
    ssh.close()

    return system_info

def send_system_info_email():
    # Email configuration
    sender_email = 'royraj4998@gmail.com'
    sender_password = 'qnmw ugdn wzjv oyse'
    receiver_emails = ['devendra.sharma@fosteringlinux.com', 'mansi.rastogi@fosteringlinux.com', 'pankaj_sgouni@fosteringlinux.com', 'ilma.parveen@fosteringlinux.com', 'nidhi_kumari@fosteringlinux.com', 'nitesh.jha@fosteringlinux.com', 'rahul_kumar@fosteringlinux.com']
    smtp_server = 'smtp.gmail.com'
    smtp_port = 587

    # Server IPs and SSH credentials
    servers = [
        {'ip': '192.168.122.65', 'username': 'rahul', 'password': 'password1'},
        {'ip': '192.168.122.68', 'username': 'thor', 'password': 'password2'},
        {'ip': '192.168.122.70', 'username': 'hulk', 'password': 'password3'},
    ]

    try:
        system_info = ""
        for server in servers:
            system_info += get_server_info(server['ip'], server['username'], server['password'])

        # Compose email message with system information
        subject = 'System Information'
        body = f"{system_info}"
        msg = MIMEText(body)
        msg['Subject'] = subject
        msg['From'] = sender_email
        msg['To'] = ', '.join(receiver_emails)

        # Connect to SMTP server and send email
        with smtplib.SMTP(smtp_server, smtp_port) as server:
            server.starttls()
            server.login(sender_email, sender_password)
            server.sendmail(sender_email, receiver_emails, msg.as_string())

        print("System information sent successfully.")
    except Exception as e:
        print(f"Error getting or sending system information: {e}")

if __name__ == "__main__":
    send_system_info_email()


```



## Remote server script

```
import smtplib
from email.mime.text import MIMEText
import paramiko

def get_system_info(server_ip, username, password):
    # SSH connection to the remote server
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    ssh.connect(server_ip, username=username, password=password)

    # Commands to fetch system information
    commands = [
        "hostname",
        "free -h",
        "df -h",
        "uptime",
        "ifconfig",
    ]

    system_info = f"Server IP: {server_ip}\n"

    for command in commands:
        stdin, stdout, stderr = ssh.exec_command(command)
        output = stdout.read().decode('utf-8')
        system_info += f"\n{command}:\n{output}\n{'='*30}"

    # Close the SSH connection
    ssh.close()

    return system_info

def send_system_info_email(sender_email, sender_password, receiver_emails, smtp_server, smtp_port, body):
    # Compose email message with system information
    subject = 'System Information'
    msg = MIMEText(body)
    msg['Subject'] = subject
    msg['From'] = sender_email
    msg['To'] = ', '.join(receiver_emails)

    # Connect to SMTP server and send email
    with smtplib.SMTP(smtp_server, smtp_port) as server:
        server.starttls()
        server.login(sender_email, sender_password)
        server.sendmail(sender_email, receiver_emails, msg.as_string())

if __name__ == "__main__":
    # Server IP and SSH credentials
    server_ip = '192.168.122.65'
    username = 'rahul'
    password = '123'

    # Email configurations
    sender_email = 'royraj4998@gmail.com'
    sender_password = 'qnmw ugdn wzjv oyse'
    receiver_emails = ['devendra.sharma@fosteringlinux.com', 'mansi.rastogi@fosteringlinux.com', 'pankaj_sgouni@fosteringlinux.com', 'ilma.parveen@fosteringlinux.com', 'nidhi_kumari@fosteringlinux.com', 'nitesh.jha@fosteringlinux.com', 'rahul_kumar@fosteringlinux.com']  # Add your recipients here
    smtp_server = 'smtp.gmail.com'
    smtp_port = 587

    try:
        # Collect system information for the specified server
        system_info = get_system_info(server_ip, username, password)

        # Send email notification with system information
        send_system_info_email(sender_email, sender_password, receiver_emails, smtp_server, smtp_port, system_info)

        print(f"System information for server {server_ip} sent successfully.")
    except Exception as e:
        print(f"Error getting or sending system information: {e}")



```



### Base server secript

```

import smtplib
from email.mime.text import MIMEText
import psutil

def get_ram_info():
    ram = psutil.virtual_memory()
    ram_info = f"Total RAM: {ram.total / (1024 ** 3):.2f} GB\n" \
                f"Used RAM: {ram.used / (1024 ** 3):.2f} GB\n" \
                f"Free RAM: {ram.available / (1024 ** 3):.2f} GB"
    return ram_info

def get_disk_info():
    disk = psutil.disk_usage('/')
    disk_info = f"Total Disk Space: {disk.total / (1024 ** 3):.2f} GB\n" \
                 f"Used Disk Space: {disk.used / (1024 ** 3):.2f} GB\n" \
                 f"Free Disk Space: {disk.free / (1024 ** 3):.2f} GB\n" \
                 f"Disk Usage Percentage: {disk.percent}%"
    return disk_info

def send_system_info_email():
    # Email configuration
    sender_email = 'royraj4998@gmail.com'
    sender_password = 'qnmw ugdn wzjv oyse'
    receiver_emails = ['devendra.sharma@fosteringlinux.com', 'mansi.rastogi@fosteringlinux.com', 'pankaj_sgouni@fosteringlinux.com', 'ilma.parveen@fosteringlinux.com', 'nidhi_kumari@fosteringlinux.com', 'nitesh.jha@fosteringlinux.com', 'rahul_kumar@fosteringlinux.com']
    smtp_server = 'smtp.gmail.com'
    smtp_port = 587

    # Compose email message with system information
    subject = 'System Information'
    body = f"RAM Information:\n{get_ram_info()}\n\nDisk Information:\n{get_disk_info()}"
    msg = MIMEText(body)
    msg['Subject'] = subject
    msg['From'] = sender_email
    msg['To'] = ', '.join(receiver_emails)

    # Connect to SMTP server and send email
    with smtplib.SMTP(smtp_server, smtp_port) as server:
        server.starttls()
        server.login(sender_email, sender_password)
        server.sendmail(sender_email, receiver_emails, msg.as_string())

if __name__ == "__main__":
    send_system_info_email()


```
