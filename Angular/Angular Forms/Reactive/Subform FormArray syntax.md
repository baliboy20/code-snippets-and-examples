## Reactive Subforms

This is a fiddly business


####formComponent.ts
````$xslt

const buckets = (namez) => ({zone: namez, a: 0, b: 0, c: 0 , d: 0, e: 0, f: 0});
@Component({
  selector: 'app-targets',
  templateUrl: './targets.component.html',
  styleUrls: ['./targets.component.scss']
})
export class TargetsComponent implements OnInit {
 targetForm: FormGroup;
  constructor( private bldr: FormBuilder) {
  }

  ngOnInit() {
    const a = new DataCollectionTarget();
    // console.log('data collection: ', a.sayHello(), a);
    const dt: DataCollectionTarget = null;
    this.targetForm = this.bldr.group({
      period: ['william', Validators.required],
      targetsForBuckets: this.bldr.array([
        this.bldr.group(buckets('inner')),
        this.bldr.group(buckets('outer'))
      ])
    });
  }

  getFormArrayControls(): any[] {

    const a: FormArray = this.targetForm.controls.targetsForBuckets  as FormArray;
    const b = a.controls;
    // const a: FormArray = this.fm.controls.web  as FormArray;
    console.log('a =>:', a);
    return a.controls; //
  }

}
````


####formComponent.html
```$xslt
<div [formGroup]="targetForm">
  <mat-form-field>
    <input type="text"
           matInput
           name=" period"
           formControlName="period">
  </mat-form-field>
  <div [formArrayName]="'targetsForBuckets'" *ngFor="let sfm of getFormArrayControls(); let idx = index">
    <ng-container [formGroupName]="idx">
<!--      {{sfm | json}}-->
      <mat-form-field>
        <input type="text" matInput formControlName="zone">
      </mat-form-field>
    </ng-container>

  </div>
</div>
```
