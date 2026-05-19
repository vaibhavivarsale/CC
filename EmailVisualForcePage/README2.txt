public class EmailController {

    // Variables connected to frontend
    public String toAddress {get; set;}
    public String subject {get; set;}
    public String body {get; set;}

    // Method to send email
    public PageReference sendEmail() {

        // Email validation
        if(toAddress == null || !toAddress.contains('@')) {

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.ERROR,
                    'Invalid Email Address'
                )
            );

            return null;
        }

        try {

            // Create email object
            Messaging.SingleEmailMessage mail =
                new Messaging.SingleEmailMessage();

            // Set recipient
            mail.setToAddresses(
                new String[] {toAddress}
            );

            // Set subject
            mail.setSubject(subject);

            // Set body
            mail.setPlainTextBody(body);

            // Send email
            Messaging.sendEmail(
                new Messaging.SingleEmailMessage[] {mail}
            );

            // Success message
            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.CONFIRM,
                    'Email Sent Successfully'
                )
            );

        }
        catch(Exception e) {

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.ERROR,
                    e.getMessage()
                )
            );
        }

        return null;
    }
}




visualforce page


<apex:page controller="EmailController">

    <apex:form >

        <apex:pageBlock title="Email Notification System">

            <!-- Messages -->
            <apex:pageMessages />

            <!-- Email -->
            <apex:pageBlockSection columns="1">

                <apex:inputText value="{!toAddress}"
                    label="Recipient Email"
                />

                <apex:inputText value="{!subject}"
                    label="Subject"
                />

                <apex:inputTextarea value="{!body}"
                    label="Message Body"
                    rows="5"
                />

            </apex:pageBlockSection>

            <!-- Button -->
            <apex:pageBlockButtons >

                <apex:commandButton value="Send Email"
                    action="{!sendEmail}"
                />

            </apex:pageBlockButtons>

        </apex:pageBlock>

    </apex:form>

</apex:page>