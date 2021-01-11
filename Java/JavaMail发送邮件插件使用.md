# 使用说明

1. 如果用j2ee自带插件，会出现"stmp"协议加载失败错误，原因是jar包版本不一致问题。
2. 最好在网上下载最新支持包。

# 附件
[activation.jar](./activation.jar)
[mail.jar](./mail.jar)

# 邮件发送使用两个beans如下：

## MyAuthenticator.java
```java
/*---------------------------------------------------------------------------------*/

package com.jsu.beans;

import javax.mail.Authenticator;
import javax.mail.PasswordAuthentication;

public class MyAuthenticator extends Authenticator {
	private String name;
	private String password;
	public MyAuthenticator(String name,String password) {
		this.name = name;
		this.password = password;
		getPasswordAuthentication();
	}
	@Override
	protected PasswordAuthentication getPasswordAuthentication() {
		// TODO Auto-generated method stub
		return new PasswordAuthentication(name, password);
	}
}
```

## TextMail.java

```java
/*---------------------------------------------------------------------------------*/
package com.jsu.beans;

import java.util.Properties;

import javax.mail.Message;
import javax.mail.MessagingException;
import javax.mail.NoSuchProviderException;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.AddressException;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;

public class TextMail {
	private String name = "";
	private String password = "";
	
	private Properties props;
	
	private Session session;
	private MimeMessage message;
	
	public TextMail(String host,String name,String password) {
		this.name = name;
		this.password = password;

		//设置SMTP主机
		props = System.getProperties();
		props.put("mail.smtp.host", host);
		props.put("mail.smtp.auth", "true");	//设置smtp验证属性
		
		//获取邮件会话对象
		MyAuthenticator auth = new MyAuthenticator(name, password);
		session = Session.getDefaultInstance(props, auth);
		
		//创建MIME邮件对象
		message = new MimeMessage(session);
	}
	
	/**
	 * 设置发件人
	 * @param from
	 */
	public void setFrom(String from){
		try {
			this.message.setFrom(new InternetAddress(from));
		} catch (AddressException e) {
			e.printStackTrace();
		} catch (MessagingException e) {
			e.printStackTrace();
		}
	}
	
	/**
	 * 设置收件人
	 * @param to
	 */
	public void setTo(String to){
		try {
			this.message.setRecipients(Message.RecipientType.TO, InternetAddress.parse(to));
		} catch (AddressException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (MessagingException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	/**
	 * 设置邮件主题
	 * @param subject
	 */
	public void setSubject(String subject){
		try {
			this.message.setSubject(subject);
		} catch (MessagingException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	public void setText(String text){
		try {
			this.message.setText(text);
		} catch (MessagingException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	/**
	 * 发送邮件
	 * @return
	 */
	public boolean send(){
		boolean flag = false;
		try {
			Transport transport = session.getTransport("smtp");
			transport.connect(props.getProperty("mail.smtp.host"), name, password);
			transport.sendMessage(message, message.getRecipients(Message.RecipientType.TO));
			transport.close();
			flag = true;
		} catch (NoSuchProviderException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (MessagingException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally{
			return flag;
		}
	}
	
	public static void main(String[] args){
		TextMail textMail = new TextMail("smtp.sina.com", "fanxiayang", "6253399110");

		//设置邮件信息
		String from = "fanxiayang@sina.com";
		String to = "fanxiayang@sina.com";
		String subject = "Text Mail Test";
		String text = "This is a text mial test!";
		
		textMail.setFrom(from);
		textMail.setTo(to);
		textMail.setSubject(subject);
		textMail.setText(text);
		
		if(textMail.send()){
			System.out.println("邮件发送成功，请到（" +to+ "）中验收！");
		}
	}

	public Session getSession() {
		return session;
	}

	public void setSession(Session session) {
		this.session = session;
	}

	public MimeMessage getMessage() {
		return message;
	}

	public void setMessage(MimeMessage message) {
		this.message = message;
	}
}
```