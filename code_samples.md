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

```typescript
  import { Component } from '@angular/core';

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

        // Define an array of signature checks
        const signatureChecks = [
          { signature: [0x50, 0x4B], type: 'ZIP archive' },
          { signature: [0x52, 0x61, 0x72, 0x21, 0x1A, 0x07, 0x00], type: 'RAR archive' },
          { signature: [0x37, 0x7A, 0xBC, 0xAF, 0x27, 0x1C], type: '7-Zip archive' },
          { signature: [0x75, 0x73, 0x74, 0x61, 0x72], type: 'Tape Archive' }, // .tar
          { signature: [0x1F, 0x8B], type: 'Gzip compressed archive' }, // .gz
          { signature: [0x1F, 0x8B, 0x08], type: 'Tarball compressed with gzip' }, // .tar.gz or .tgz
          { signature: [0x42, 0x5A, 0x68], type: 'Bzip2 compressed archive' }, // .bz2
          { signature: [0x42, 0x5A, 0x68, 0x39], type: 'Tarball compressed with bzip2' }, // .tar.bz2 or .tbz2
          { signature: [0xFD, 0x37, 0x7A, 0x58, 0x5A, 0x00], type: 'XZ compressed archive' }, // .xz
          { signature: [0xFD, 0x37, 0x7A, 0x58, 0x5A, 0x00, 0x00], type: 'Tarball compressed with XZ' }, // .tar.xz or .txz
          { signature: [0x1F, 0x9D], type: 'Tarball compressed with compress' }, // .tar.Z
          { signature: [0x4D, 0x53, 0x43, 0x46], type: 'Cabinet archive' }, // .cab
          { signature: [0x43, 0x44, 0x30, 0x30, 0x31], type: 'ISO disc image' }, // .iso
          { signature: [0x78, 0x61, 0x72, 0x21], type: 'Apple Disk Image' }, // .dmg
          { signature: [0x50, 0x4B, 0x03, 0x04], type: 'Java Archive' }, // .jar
          { signature: [0x1F, 0x9D, 0x01, 0x00], type: 'Tarball compressed with Zstandard' }, // .tar.zst or .zst
          { signature: [0x53, 0x49, 0x54, 0x21], type: 'StuffIt archive' }, // .sit
          { signature: [0x1E, 0xEC], type: 'ACE archive' }, // .ace
          { signature: [0x60, 0xEA], type: 'ARJ archive' }, // .arj
          { signature: [0x4C, 0x5A, 0x48], type: 'LZH archive' }, // .lzh or .lha
          // Add more signature checks for other archive types
// ...
{ signature: [0x75, 0x73, 0x74, 0x61, 0x72, 0x00, 0x30, 0x30, 0x75, 0x73, 0x74, 0x61, 0x72, 0x00, 0x00], type: 'Tape Archive' }, // .tar
// ...

        ];

        // Try detecting various archive formats
        const detectedType = this.detectSignature(uint8Array, signatureChecks);
        resolve(detectedType);
      };

      reader.readAsArrayBuffer(file);
    });
  }

  detectSignature(data: Uint8Array, signatureChecks: { signature: number[]; type: string }[]): string | null {
    for (const check of signatureChecks) {
      if (this.hasSignature(data, check.signature)) {
        return check.type;
      }
    }
    return null;
  }

  hasSignature(data: Uint8Array, signature: number[]): boolean {
    if (data.length >= signature.length) {
      for (let i = 0; i < signature.length; i++) {
        if (data[i] !== signature[i]) {
          return false;
        }
      }
      return true;
    }
    return false;
  }
}

```

```typescript

  function flattenJson(data: any): any {
  const result: any = {};

  function recurse(current: any, parentKey?: string): void {
    for (const key in current) {
      if (current.hasOwnProperty(key)) {
        const newKey = parentKey ? `${parentKey}.${key}` : key;

        if (typeof current[key] === 'object' && !Array.isArray(current[key])) {
          recurse(current[key], newKey);
        } else {
          result[newKey] = current[key];
        }
      }
    }
  }

  recurse(data);

  return result;
}

// Your nested JSON data
const nestedJson = {
  "success": true,
  "ip": "203.189.181.32",
  "type": "IPv4",
  "country": {
    "code": "IN",
    "name": "India"
  },
  "location": {
    "lat": 21.9974,
    "lon": 79.0011
  },
  "timeZone": "Asia/Kolkata",
  "asn": {
    "number": 38174,
    "name": "Capgemini Technology Services India Limited",
    "network": "203.189.180.0/22"
  }
};

// Flatten the nested JSON
const flattenedJson = flattenJson(nestedJson);
console.log(flattenedJson);

{ signature: [0x75, 0x73, 0x74, 0x61, 0x72, 0x00, 0x30, 0x30], type: 'Tape Archive' }, // .tar
// ...
{ signature: [0x75, 0x73, 0x74, 0x61, 0x72, 0x00, 0x30, 0x30], type: 'Tape Archive' }, // .tar
// ...


```
