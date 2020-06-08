## Custom component Material design Form control based upon  Angular Material documentation
https://material.angular.io/guide/creating-a-custom-form-field-control
this one is work in progress
```
import {Component, OnInit, Optional, Self} from '@angular/core';
import {ControlValueAccessor, NG_VALUE_ACCESSOR, NgControl} from "@angular/forms";
import {MatFormFieldControl} from "@angular/material/form-field";
import {FocusMonitor} from "@angular/cdk/a11y";

@Component({
  selector: 'app-thousands-input',
  templateUrl: './thousands-input.component.html',
  styleUrls: ['./thousands-input.component.scss'],
  providers: [
    {provide: MatFormFieldControl, useExisting: ThousandsInputComponent},
    // {provide: NG_VALUE_ACCESSOR, useExisting: ThousandsInputComponent, multi: true}
  ]
})
export class ThousandsInputComponent implements OnInit,
  MatFormFieldControl<ThousandsInputComponent>,
  ControlValueAccessor {

  constructor(

    private _focusMonitor: FocusMonitor,
    public ngControl: NgControl
  ) {
    if (this.ngControl) {
      this.ngControl.valueAccessor = this;
    }
  }


  stateChanges: import("rxjs").Observable<void>;
  id: string;
  placeholder: string;
  focused: boolean;
  empty: boolean;
  shouldLabelFloat: boolean = true;
  required: boolean;
  disabled: boolean;
  errorState: boolean;
  controlType?: string;
  autofilled?: boolean;

  setDescribedByIds(ids: string[]): void {
    throw new Error("Method not implemented.");
  }

  onContainerClick(event: MouseEvent): void {
   if((event.target as Element).tagName.toLowerCase() === 'input') {
     const ele = (event.target as HTMLElement).focus();
   }
  }

  value;

  ngOnInit(): void {
  }

  onChange = (val) => {
  }

  registerOnChange(fn: any): void {
    this.onChange = fn;

  }

  registerOnTouched(fn: any): void {
  }

  writeValue(obj: any): void {
    this.value = obj;
  }

  modelchanged($event: any) {
    this.onChange($event);
  }

  onblur($event: FocusEvent) {

    let res = '';
    const val = +$event.target['value'];
    if (typeof val === 'number') {
      const num = Math.trunc(val) + '';
      let arr = num.split('');
      arr = arr.reverse();
      console.log('array', arr);
      let flag = false;

      arr.forEach((val, idx, arr) => {
        if (arr.length > 3) {
          console.log('ans', idx + 1, (idx + 1) % 3)
          if ((idx + 1) % 3 === 0) {
            res = ',' + val + res;
          } else {
            res = val + res;
          }
        } else {

        }

        //000 000 000
      });
      res += '.00';
      this.value = res;
    }

  }

  onFocus($event: FocusEvent) {
    this.value = this.value.replace(',', '');
  }
}

```
html is like
```
<input type="text"
       matInput
       (blur)="onblur($event)"
       (focus)="onFocus($event)"
       (ngModelChange)="modelchanged($event)"
       [(ngModel)]="value">
```


### The container component

``` 

@Component({
  selector: 'app-formz',
  templateUrl: './formz.component.html',
  styleUrls: ['./formz.component.scss']
})
export class FormzComponent implements OnInit {
fb: FormGroup = this.fbg.group({telno:'87654', name: 'will'})
  constructor(
    private matSnackBar: MatSnackBar,
    private fbg: FormBuilder) { }

  ngOnInit(): void {

  this.fb.valueChanges
    .pipe(tap(console.log))
    .subscribe(a => this.matSnackBar.open(a))
  }

}
```

```
<div [formGroup]="fb">

  <mat-form-field appearance="outline">
    <mat-label>Surname</mat-label>
    <input type="text" matInput formControlName="name">

  </mat-form-field>
  <mat-form-field appearance="outline">
    <mat-label>telno</mat-label>
    <app-thousands-input formControlName="telno"></app-thousands-input>
  </mat-form-field>

</div>
```
