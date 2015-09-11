#!/usr/bin/python
# -*- coding: UTF-8 -*-
# coding=UTF-8
import email
import os
import mimetypes
from email.MIMEMultipart import MIMEMultipart
from email.MIMEText import MIMEText
from email.MIMEImage import MIMEImage
from email.header import Header
import smtplib

class Mail:
    def __init__(self):
        self.default_smtpserver = "smtp.exmail.qq.com"#发送服务器
        self.default_username = '***@**.com'#用户名
        self.default_password = '*****'#密码
        self.default_sender = "%s ****" % Header("service","utf-8")#发件人
        #self.default_receiver = ""#收件人

    def sendEmail(self,title,msg,receiver,sender='',username='',password='',smtpserver=''):
        if not (msg and title and receiver):
            return False;
        if(sender):
            self.default_sender = sender
        if(username and password):
            self.default_username = username
            self.default_password = password
        if(smtpserver):
            self.default_smtpserver = smtpserver
            
        self.getEmailInfo(title,msg,receiver)
            

    def getEmailInfo(self,title,msg,receiver):
        strFrom = self.default_sender
        if(isinstance(receiver, list)):
            strTo = ';'.join(receiver)
        else:
            strTo = receiver
        server = self.default_smtpserver
        user = self.default_username
        passwd = self.default_password
        if not (server and user and passwd) :
                print 'incomplete login info, exit now'
                return
        # 设定root信息
        msgRoot = MIMEMultipart('related')
        #if not isinstance(subject,unicode):
        #subject = unicode(subject)
        #subject = Header(title,"utf-8")
        msgRoot['subject'] = Header(title,"utf-8")
        msgRoot['from'] = strFrom
        msgRoot['to'] = strTo
        msgRoot['X-Priority'] =  '''3'''
        msgRoot['X-MSMail-Priority'] =  '''Normal'''
        msgRoot['X-Mailer'] =  '''Microsoft Outlook Express 6.00.2900.2180'''
        msgRoot['X-MimeOLE'] =  '''Produced By Microsoft MimeOLE V6.00.2900.2180'''
        msgRoot["Accept-Language"]="zh-CN"
        msgRoot["Accept-Charset"]="UTF-8"

        msgRoot.preamble = 'This is a multi-part message in MIME format.'
        # Encapsulate the plain and HTML versions of the message body in an
        # 'alternative' part, so message agents can decide which they want to display.
        msgAlternative = MIMEMultipart('alternative')
        msgRoot.attach(msgAlternative)

        #设定HTML信息
        msgText = MIMEText(msg, 'html', 'UTF-8')
        msgAlternative.attach(msgText)
        try:
            #发送邮件
            smtp = smtplib.SMTP()
            #设定调试级别，依情况而定
            smtp.connect(server)
            smtp.login(user, passwd)
            #receiver如果是多个 那么receiver必须是list，不是string，否则只能发给第一个人
            smtp.sendmail(strFrom, receiver, msgRoot.as_string())
            smtp.quit()
        except Exception, e:
            print str(e)

        return True




