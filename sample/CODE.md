````Typescript
import { Component, ViewChild, ElementRef } from '@angular/core';

@Component({
  selector: 'app-file-upload',
  template: `
    <input type="file" #fileInput (change)="onFileSelected($event)" />
  `,
  styles: [],
})
export class FileUploadComponent {
  @ViewChild('fileInput', { static: false }) fileInput: ElementRef;

  onFileSelected(event: any): void {
    const file: File = event.target.files[0];

    if (file) {
      this.verifyFileIsZip(file).then((isZip) => {
        if (isZip) {
          console.log('The file is a zip file.');
        } else {
          console.log('The file is not a zip file.');
        }
      });
    }
  }

  verifyFileIsZip(file: File): Promise<boolean> {
    return new Promise<boolean>((resolve) => {
      const reader = new FileReader();

      reader.onloadend = () => {
        // Check the magic numbers to identify the file type
        const arr = new Uint8Array(reader.result as ArrayBuffer).subarray(0, 4);
        let header = '';
        for (let i = 0; i < arr.length; i++) {
          header += arr[i].toString(16);
        }

        // Zip file magic number: 504B0304
        const isZip = header === '504b0304';
        resolve(isZip);
      };

      reader.readAsArrayBuffer(file.slice(0, 4));
    });
  }
}
````
