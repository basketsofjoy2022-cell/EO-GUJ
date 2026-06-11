# One-Time Setup: Auto-Save PDFs to Google Drive

Takes about 5 minutes. Do this while logged in as **eogujarat@gmail.com**.

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
9. Send that URL back to Claude (or paste it into `index.html` where it says `PASTE_APPS_SCRIPT_URL_HERE`)

Done. Every submission now lands as a PDF in a Drive folder called **EO GST Declarations**, named like:
`GST-Declaration-Anshul-Maskara-2026-06-11.pdf`

## The Code (paste all of it)

```javascript
const FOLDER_NAME = "EO GST Declarations";

function doPost(e) {
  try {
    const data = JSON.parse(e.postData.contents);
    if (!data.pdfBase64 || !data.fileName) throw new Error("Missing PDF data");

    const folder = getFolder_();
    const bytes = Utilities.base64Decode(data.pdfBase64);
    const blob = Utilities.newBlob(bytes, "application/pdf", data.fileName);
    const file = folder.createFile(blob);

    return ContentService
      .createTextOutput(JSON.stringify({ success: true, fileUrl: file.getUrl() }))
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

function getFolder_() {
  const it = DriveApp.getFoldersByName(FOLDER_NAME);
  return it.hasNext() ? it.next() : DriveApp.createFolder(FOLDER_NAME);
}
```
