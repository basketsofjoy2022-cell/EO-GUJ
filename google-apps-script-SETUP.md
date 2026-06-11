# One-Time Setup: Auto-Save to Drive + Email with PDF Attached

Takes about 5 minutes. Do this while logged in as **eogujarat@gmail.com**.

This one script does both jobs on every submission:

1. Saves the signed PDF to a Drive folder called **EO GST Declarations** (named like `GST-Declaration-Anshul-Maskara-2026-06-11.pdf`)
2. Emails eogujarat@gmail.com with the member's details and the **PDF attached**

## Steps

1. Open https://script.google.com and click **New project**
2. Delete any code in the editor and paste the full code below
3. Click the save icon, name the project `EO GST Declarations Receiver`
4. Click **Deploy > New deployment**
5. Click the gear icon, choose **Web app**
6. Set:
   - Description: `EO form receiver`
   - Execute as: **Me (eogujarat@gmail.com)**
   - Who has access: **Anyone**
7. Click **Deploy**, approve the permission popup (click Advanced > Go to project if Google warns)
8. Copy the **Web app URL** (looks like `https://script.google.com/macros/s/XXXX/exec`)
9. Send that URL back to Claude (or paste it into `index.html` in place of `PASTE_APPS_SCRIPT_URL_HERE`)

## The Code (paste all of it)

```javascript
const FOLDER_NAME = "EO GST Declarations";
const SEND_TO = "eogujarat@gmail.com";

function doPost(e) {
  try {
    const data = JSON.parse(e.postData.contents);
    if (!data.pdfBase64 || !data.fileName) throw new Error("Missing PDF data");

    // 1. Save PDF to the central Drive folder
    const folder = getFolder_();
    const blob = Utilities.newBlob(
      Utilities.base64Decode(data.pdfBase64),
      "application/pdf",
      data.fileName
    );
    const file = folder.createFile(blob);

    // 2. Email with the PDF attached
    let emailed = false;
    try {
      MailApp.sendEmail({
        to: SEND_TO,
        subject: "GST Declaration - " + (data.member || "Member") +
                 (data.company ? " (" + data.company + ")" : ""),
        htmlBody:
          "<p>New GST declaration submitted.</p>" +
          "<table border='1' cellpadding='6' style='border-collapse:collapse'>" +
          row_("Name of Member", data.member) +
          row_("Father's / Parent's Name", data.parent) +
          row_("Residential Address", data.address) +
          row_("Company / Entity", data.company) +
          row_("Date", data.date) +
          row_("Place", data.place) +
          row_("Name (as signed)", data.signName) +
          "</table>" +
          "<p>Signed PDF attached. Drive copy: <a href='" + file.getUrl() + "'>" +
          data.fileName + "</a></p>",
        attachments: [blob]
      });
      emailed = true;
    } catch (mailErr) {}

    return ContentService
      .createTextOutput(JSON.stringify({ success: true, emailed: emailed, fileUrl: file.getUrl() }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ success: false, error: String(err) }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet() {
  return ContentService
    .createTextOutput(JSON.stringify({ status: "EO GST Declarations receiver is live" }))
    .setMimeType(ContentService.MimeType.JSON);
}

function row_(k, v) {
  return "<tr><td><b>" + k + "</b></td><td>" + (v || "") + "</td></tr>";
}

function getFolder_() {
  const it = DriveApp.getFoldersByName(FOLDER_NAME);
  return it.hasNext() ? it.next() : DriveApp.createFolder(FOLDER_NAME);
}
```
