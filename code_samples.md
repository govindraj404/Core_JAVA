```typescript

// file-uploader.component.ts
import { Component, EventEmitter, Output } from '@angular/core';
import * as FileType from 'file-type';

@Component({
  selector: 'app-file-uploader',
  template: `
    <input type="file" multiple (change)="handleFileInputChange($event)">
  `,
})
export class FileUploaderComponent {
  @Output() anyFileValidated = new EventEmitter<boolean>();

  handleFileInputChange(event: any): void {
    const files: FileList = event.target.files;
    let anyValid = false;

    for (let i = 0; i < files.length; i++) {
      const file = files[i];

      // Use file-type to detect the file type
      const fileType = this.detectFileType(file);

      // Check if the file is a valid archive
      if (this.isValidArchive(fileType) && this.hasValidExtension(file.name)) {
        anyValid = true;
        break; // Break out of the loop if any valid archive is found
      }
    }

    this.anyFileValidated.emit(anyValid);
  }

  detectFileType(file: File): FileType.MimeTypeResult | null {
    return new Promise((resolve) => {
      const reader = new FileReader();

      reader.onload = (e) => {
        const buffer = e.target?.result as ArrayBuffer;

        // Use file-type to detect the file type
        const fileType = FileType(buffer);

        resolve(fileType);
      };

      // Read the first 4100 bytes of the file
      reader.readAsArrayBuffer(file.slice(0, 4100));
    });
  }

  isValidArchive(fileType: FileType.MimeTypeResult | null): boolean {
    // Check if the detected MIME type corresponds to a valid archive type
    return (
      fileType &&
      (fileType.mime === 'application/zip' ||
        fileType.mime === 'application/x-rar-compressed' ||
        fileType.mime === 'application/x-7z-compressed' ||
        fileType.mime === 'application/x-tar' ||
        fileType.mime === 'application/gzip' ||
        fileType.mime === 'application/x-bzip2' ||
        fileType.mime === 'application/x-xz' ||
        fileType.mime === 'application/x-compress' ||
        fileType.mime === 'application/vnd.ms-cab-compressed' ||
        fileType.mime === 'application/x-iso9660-image' ||
        fileType.mime === 'application/x-apple-diskimage' ||
        fileType.mime === 'application/java-archive' ||
        fileType.mime === 'application/zstd' ||
        fileType.mime === 'application/x-stuffit' ||
        fileType.mime === 'application/x-ace-compressed' ||
        fileType.mime === 'application/x-arj' ||
        fileType.mime === 'application/x-lzh-compressed')
    );
  }

  hasValidExtension(fileName: string): boolean {
    // Check if the file has a valid extension (customize as needed)
    const validExtensions = ['.zip', '.rar', '.7z', '.tar', '.gz', '.tar.gz', '.bz2', '.tar.bz2', '.xz', '.tar.xz', '.tar.Z', '.cab', '.iso', '.dmg', '.jar', '.zst', '.tar.zst', '.sit', '.ace', '.arj', '.lzh', '.lha'];
    const fileExtension = fileName.toLowerCase().slice(((fileName.lastIndexOf(".") - 1) >>> 0) + 2);

    return validExtensions.includes(fileExtension);
  }
}
```

```typescript

// app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <app-file-uploader (anyFileValidated)="handleAnyFileValidation($event)"></app-file-uploader>
  `,
})
export class AppComponent {
  handleAnyFileValidation(anyValid: boolean): void {
    if (anyValid) {
      console.log('At least one file is a valid archive!');
    } else {
      console.log('No valid archive files found.');
    }
  }
}



```


```typescript
  import { Component } from '@angular/core';
import { saveAs } from 'file-saver';
import * as pako from 'pako';

@Component({
  selector: 'app-root',
  template: `
    <input type="file" (change)="handleFileInputChange($event)">
  `,
})
export class AppComponent {
  handleFileInputChange(event: any): void {
    const files: FileList = event.target.files;

    for (let i = 0; i < files.length; i++) {
      const file = files[i];
      this.detectArchiveType(file).then((archiveType) => {
        if (archiveType) {
          console.log(`${file.name} is a ${archiveType} archive.`);
        } else {
          console.log(`${file.name} is not a recognized archive.`);
        }
      });
    }
  }

  async detectArchiveType(file: File): Promise<string | null> {
    return new Promise<string | null>((resolve) => {
      const reader = new FileReader();

      reader.onload = (e) => {
        const arrayBuffer = e.target?.result as ArrayBuffer;
        const uint8Array = new Uint8Array(arrayBuffer);

        // Try detecting various archive formats
        if (this.hasZipSignature(uint8Array)) {
          resolve('ZIP');
        } else if (this.hasRarSignature(uint8Array)) {
          resolve('RAR');
        } else if (this.has7ZipSignature(uint8Array)) {
          resolve('7-Zip');
        } else {
          resolve(null);
        }
      };

      reader.readAsArrayBuffer(file);
    });
  }

  hasZipSignature(data: Uint8Array): boolean {
    // Check for ZIP file signature (first two bytes)
    return data.length >= 2 && data[0] === 0x50 && data[1] === 0x4B;
  }

  hasRarSignature(data: Uint8Array): boolean {
    // Check for RAR file signature (first seven bytes)
    return (
      data.length >= 7 &&
      data[0] === 0x52 &&
      data[1] === 0x61 &&
      data[2] === 0x72 &&
      data[3] === 0x21 &&
      data[4] === 0x1A &&
      data[5] === 0x07 &&
      data[6] === 0x00
    );
  }

  has7ZipSignature(data: Uint8Array): boolean {
    // Check for 7-Zip file signature (first six bytes)
    return (
      data.length >= 6 &&
      data[0] === 0x37 &&
      data[1] === 0x7A &&
      data[2] === 0xBC &&
      data[3] === 0xAF &&
      data[4] === 0x27 &&
      data[5] === 0x1C
    );
  }
}

```
