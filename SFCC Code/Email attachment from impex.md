# Isml template
```
<iscontent type="multipart/mixed; boundary=------------000001030701020908040900" compact="false"/>

  

<isset name="fileContent" value="${pdict.data}" scope="page"/>

--------------000001030701020908040900

Content-Type: text/html; charset=UTF-8

  

${pdict.body}

  

--------------000001030701020908040900

Content-Type: text/xml;charset=ISO-8859-1

Content-Description: ${pdict.name}

Content-Disposition: attachment; filename="${pdict.name}"; size=${fileContent.length}; creation-date="${(new Date()).toISOString()}"; modification-date="${(new Date()).toISOString()}"

Content-Transfer-Encoding: base64

  

${fileContent}

--------------000001030701020908040900--
```

# JS Script
```
/**

 *   The Product Review Logger to tracking rating review data lost error.

 *    @input ActiveFilePath         : String            CSV file name.

 */

  

function execute(pdict) {

    var Logger = require('dw/system/Logger');

    var HashMap = require('dw/util/HashMap');

    var Resource = require('dw/web/Resource');

    var Mail = require('dw/net/Mail');

    var Template = require('dw/util/Template');

    var ContentMgr = require('dw/content/ContentMgr');

  

    try {

        var currentSiteID = require('dw/system/Site').getCurrent().getID();

        var instanceHostname = require('dw/system/System').getInstanceHostname()

        var mail = new Mail();

        var template = new Template('mail/emailattachment');

        var emailTo = require('configuration').getValue('mailRecipientForRatingReview').split(',');

  

        var emailObj = new HashMap();

        var attachmentData = createAttachmentData(pdict.ActiveFilePath);

        var bodyContent = ContentMgr.getContent('rrApprovalAttachment');

        emailObj.put("body", bodyContent.custom.body);

        emailObj.put("data", attachmentData);

        emailObj.put("name", pdict.ActiveFilePath);

  

        emailTo.forEach(function (emailAddress) {

            mail.addTo(emailAddress);

        });

  

        mail.setFrom(require('utils/email').getDefaultEmailFrom());

        mail.setSubject(Resource.msgf('rr.email.attachment.subject', 'ratingsandreviews', '', currentSiteID, instanceHostname));

        mail.setContent(template.render(emailObj));

        mail.send();

    } catch (e) {
        Logger.error('Error email not sent :{0}', e.message);

        return PIPELET_ERROR;

    }

  

    return PIPELET_NEXT;

}

  

/**

* create attachment data for email.

* @param ActiveFilePath String: CSV file path.

* @returns String: Base64 encoded string

*/

function createAttachmentData(ActiveFilePath) {

  

    try {

        var Logger = require('dw/system/Logger');

        var File = require('dw/io/File');

        var FileReader = require('dw/io/FileReader');

        var file = new File(File.IMPEX + "/src/upload/activedata/"+ActiveFilePath);

        var fileReader = new FileReader(file, 'ISO-8859-1');

        var csv = fileReader.string;

        var csvBase64 = require('dw/util/StringUtils').encodeBase64(csv, 'ISO-8859-1');

    } catch (error) {

        Logger.error('Error in createAttachmentData, Message : {0}', error.message);

        return PIPELET_ERROR;

    }

  

    return csvBase64;

}
```