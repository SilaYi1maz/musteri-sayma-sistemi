Google Sheets Apps Script kodları bu klasördedir.
function doGet(e) {
  var ss = SpreadsheetApp.openById("https://script.google.com/u/0/home/projects/1kWeIifr-c25A2p4fKOTAJieP4Lue_MKLFEaUwOQeTZTnWNMybHtGrk0Z/edit");
  var sheet = ss.getSheets()[0];

  var musteri = e.parameter.sayi;

  var now = new Date();
  var tarih = Utilities.formatDate(now, "Europe/Istanbul", "dd.MM.yyyy");
  var saat  = Utilities.formatDate(now, "Europe/Istanbul", "HH:mm:ss");

  sheet.appendRow([tarih, saat, musteri]);

  return ContentService.createTextOutput("OK");
}
