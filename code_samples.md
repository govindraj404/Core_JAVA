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
