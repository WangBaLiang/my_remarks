---
layout: post
title: Python常见问题解决
categories: [编程]
tags: [Python]
disqus: y
---
# 发送邮件问题



## 代码

    import smtplib
    from email.mime.text import MIMEText
    from email.mime.multipart import MIMEMultipart
    from email.mime.base import MIMEBase
    from email.mime.text import MIMEText
    from email.utils import formatdate
    from email import encoders

    def send_email(to_addresses, subject, content, content_type='html', charset='utf-8', files=[]):
        """
        发送邮件
        :to_addresses: 收件人邮箱，以;分隔
        :subject: 邮件主题
        :content: 邮件正文
        :param files: 文件名列表
        :return:
        """
        server_host = 'XXXX'
        server_port = 25
        user_name = 'xxxx'
        password = 'xxx'
        timeout = 3
        from_address = user_name

        message = MIMEMultipart()
        for filename in files:
            msg = MIMEBase('application', 'octet-stream')
            with open(filename, 'rb') as f:
                msg.set_payload(f.read())
            encoders.encode_base64(msg)
            msg.add_header('Content-Disposition', 'attachment', filename=filename)
            message.attach(msg)

        message.attach(MIMEText(content, _subtype=content_type, _charset=charset))
        message['Subject'] = subject
        message['From'] = from_address
        message['To'] = ';'.join(to_addresses)
        client = smtplib.SMTP(server_host, server_port, timeout=timeout)
        client.starttls()
        client.login(user_name, password)
        client.sendmail(from_address, to_addresses, message.as_string())
        client.quit()


# 文件操作

    # 文件复制
    import shutil
    shutil.copyfile(src_file, dst_file)
    shutil.copy(src_file, dst_file_or_folder)

    # 复制包括源文件属性
    shutil.copy2(src_file, dst_file)

    # 移动文件或目录
    shutil.move(src_file_or_folder, dst_file_or_folder)

    # symlinks: True复制链接，False复制链接所指文件
    # dst_folder: 需是新文件夹，copytree会新建目录
    shutil.copytree(src_folder, dst_folder, symlinks=False, ignore=None)

    # 删除整个文件夹，可以非空
    shutil.rmtree(folder)

    # 创建目录
    os.mkdir(path)
    # mkdir -P
    os.makedirs(path)

    # 遍历目录
    # 第一种
    for root, dirs, files in os.walk(path)
        # files 该目录及子目录下所有文件
    # 第二种
    def traverse_path(rootDir):
        for lists in os.listdir(rootDir):
            path = os.path.join(rootDir, lists)
            print path
            if os.path.isdir(path):
                traverse_path(path)

    # 获取目录的文件名
    os.path.basename(path)

