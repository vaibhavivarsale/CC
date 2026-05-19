with attachment


public class EmailSender {
    
    public static void sendEmailWithAttachment() {

        Messaging.SingleEmailMessage mail =
            new Messaging.SingleEmailMessage();

        mail.setToAddresses(
            new String[] {'vaibhavisv2510@gmail.com'}
        );

        mail.setSubject('Email With Attachment');

        mail.setPlainTextBody(
            'Please find attachment.'
        );

        Messaging.EmailFileAttachment attachment =
            new Messaging.EmailFileAttachment();

        attachment.setFileName('sample.txt');

        attachment.setBody(
            Blob.valueOf('This is attachment content')
        );

        mail.setFileAttachments(
            new Messaging.EmailFileAttachment[] { attachment }
        );

        Messaging.sendEmail(
            new Messaging.SingleEmailMessage[] { mail }
        );

        System.debug('Attachment Mail Sent');
    }
}



without attachment:



public class EmailSender {

    public static void sendEmailMethod() {

        Messaging.SingleEmailMessage mail =
            new Messaging.SingleEmailMessage();

        mail.setToAddresses(
            new String[] {'test@gmail.com'}
        );

        mail.setSubject('Apex Email');

        mail.setPlainTextBody(
            'Hello from Salesforce Apex'
        );

        Messaging.sendEmail(
            new Messaging.SingleEmailMessage[] { mail }
        );

        System.debug('Mail Sent');
    }
}