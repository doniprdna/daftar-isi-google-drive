# daftar isi google drive
berikut adalah script yang  digunakan untuk membuat daftar isi folder/file yang ada dalam google drive

copy paste script di bawah ini:
```function enumerateDriveContents() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  sheet.clear(); // Hapus konten lama di sheet
  sheet.setColumnWidths(1, 25, 225); // Set lebar kolom ke 225 untuk kolom 1 sampai 25
  const root = DriveApp.getRootFolder();
  drillDown(root, sheet);
}

function drillDown(folder, sheet, col = 1, row = 2) {
  const folders = folder.getFolders();
  const files = folder.getFiles();

  const folderList = [];
  while (folders.hasNext()) {
    const currFolder = folders.next();
    folderList.push(currFolder);
  }

  // Urutkan folder berdasarkan nama alfabetik atau numerik
  folderList.sort(function(a, b) {
    return compareNames(a.getName(), b.getName());
  });

  // Warna latar belakang sesuai dengan tingkat level folder
  const backgroundColors = ['#ffffff', '#e9f7fc', '#d5e8d4', '#f3e5f5', '#ffebcc', '#e5e5e5', '#f9e79f', '#f1948a', '#bb8fce', '#aed6f1', '#d7bde2', '#f7dc6f', '#f0b27a', '#a9dfbf', '#aed6f1', '#a3e4d7', '#f0b27a', '#f1948a', '#fadbd8', '#aed6f1', '#d5e8d4', '#d5f5e3', '#f0b27a', '#ffebcc'];

  // Tulis judul kolom pada baris pertama dan beri fill color yang sesuai
  sheet.getRange(1, col, 1, 1).setValue('Level ' + col);
  sheet.getRange(1, col, 1, 1).setBackground(backgroundColors[col - 1]);

  // Set tinggi baris, vertical alignment, dan wrap text untuk judul kolom
  sheet.setRowHeights(1, 1, 50); // Set tinggi baris 1 ke 50
  sheet.getRange(1, col, 1, 1).setVerticalAlignment("top"); // Set vertical alignment ke top
  sheet.getRange(1, col, 1, 1).setWrap(true); // Set wrap text
  
  // Tulis folder ke dalam sheet dan rekursi untuk subfolder
  for (const currFolder of folderList) {
    sheet.getRange(row, col, 1, 1).setValue(createHyperlink(currFolder));
    sheet.getRange(row, col, 1, 1).setBackground(backgroundColors[col - 1]); // Warna latar belakang sesuai dengan tingkat level
    sheet.getRange(row, col, 1, 1).setVerticalAlignment("top"); // Set vertical alignment ke top
    sheet.getRange(row, col, 1, 1).setWrap(true); // Set wrap text
    row = drillDown(currFolder, sheet, col + 1, row);
  }

  const fileList = [];
  while (files.hasNext()) {
    const currFile = files.next();
    fileList.push(currFile);
  }

  // Urutkan file berdasarkan nama alfabetik atau numerik
  fileList.sort(function(a, b) {
    return compareNames(a.getName(), b.getName());
  });

  // Tulis file ke dalam sheet
  for (const currFile of fileList) {
    sheet.getRange(row, col, 1, 1).setValue(createHyperlink(currFile));
    sheet.getRange(row, col, 1, 1).setBackground(backgroundColors[col - 1]); // Warna latar belakang sesuai dengan tingkat level
    sheet.getRange(row, col, 1, 1).setVerticalAlignment("top"); // Set vertical alignment ke top
    sheet.getRange(row, col, 1, 1).setWrap(true); // Set wrap text
    row++;
  }

  return row;
}

function createHyperlink(item) {
  return `=HYPERLINK("${item.getUrl()}", "${item.getName()}")`;
}

function compareNames(name1, name2) {
  var regex = /^\d+/; // Cek apakah nama dimulai dengan angka
  var name1IsNumeric = regex.test(name1);
  var name2IsNumeric = regex.test(name2);

  if (name1IsNumeric && name2IsNumeric) {
    // Jika keduanya dimulai dengan angka, urutkan sebagai angka
    return parseInt(name1) - parseInt(name2);
  } else if (name1IsNumeric) {
    // Jika name1 dimulai dengan angka, name1 akan muncul sebelum name2
    return -1;
  } else if (name2IsNumeric) {
    // Jika name2 dimulai dengan angka, name2 akan muncul sebelum name1
    return 1;
  } else {
    // Jika keduanya dimulai dengan huruf, urutkan secara alfabetik
    return name1.localeCompare(name2);
  }
}

```
