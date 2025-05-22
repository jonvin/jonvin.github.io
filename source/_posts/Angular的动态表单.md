---
title: Angular的动态表单
date: 2021-08-18 09:10:06
tags:
---

前言：到新的公司写 angular 也有 2 个月了，众所周知 Angular 的学习曲线是非常陡峭的，而且在网上找学习的博客也是比 vue 和 react 要少很多，在写 Angular 的过程中，生命周期和父子组件传值，这些基本的操作跟 vue 也是大同小异。值得眼前一亮的便是它的动态表单。

FormGroup 和 FormArray 的区别

### FormGroup

跟踪一组 FormControl 实例的值和有效状态。有对应的 key 值； 添加删除对应的方法分别为 addControl / removeControl ;

### FormArray

跟踪一个控件数组和有效状态，控件可以是 FormControl 、FormGroup 或者是 FormArray 的实例。
无对应的 key 值；添加删除的方法分别为 push/removeAt

### FormGroup 的基本使用例子

demo.componemt.html

```html

<nz-spin [nzSpinning]='loading' nzTip='加载中 。。。'>
    <!--这一组formGroup 的名称为name -->
    <form nz-form [formGroup]='form'>
        <nz-form-item>
            <nz-form-label [nzSpan]='4' nzRequired>模版名称</nz-form-label>
            <nz-form-control [nzSpan]='16'>
                <!-- 通过 formControlName 来绑定监听input值发生变化-->
                <input formControlName='templateName' nz-input placeholder='请输入' />
                <nz-form-explain *ngIf="form.get('templateName')?.dirty && form.get('templateName')?.hasError('require')">
                    模版名称必填
                <nz-form-explain>
            <nz-form-control>
        <nz-form-item>

        <nz-form-item>
            <nz-form-label [nzSpan]='4' nzRequired>模版内容</nz-form-label>
            <nz-form-control [nzSpan]='16'>
                <textarea formControlName='templateContent' placeholder='请输入'> </textarea>
                <nz-form-explain *ngIf="form.get('templateContent')?.dirty && form.get('templateContent')?.hasError('required')">
                    模版内容必填
                </nz-form-explain>
            </nz-form-control>
        </nz-form-item>

        <nz-form-item>
           <nz-form-label [nzSpan]='4' nzRequired>选择国家</nz-form-label>
           <nz-form-control>
               <nz-checkbox-group formControlName='country'> </nz-checkbox-group>
           </nz-form-control>
           <nz-form-explain *ngIf="form.get('country')?.dirty && form.get('country')?.hasError('required')">
                国家至少选择一个
           </nz-form-explain>
        </nz-form-item>

        <nz-form-item>
            <nz-form-label [nzSpan]='4' nzRequired>是否启用</nz-form-label>
            <nz-form-control [nzSpan]='16'>
                <nz-switch formControlName='status'></nz-switch>

            </nz-form-control>
        </nz-form-item>
    </form>
</nz-spin>
```

demo.componemt.ts

```ts
import { FormBuilder } from "@angular/forms";

export class DemoComponent implements OnInit {
    form: FormGroup;
    requestFinanceTypesOption = [];

    constructor(private fb: FormBuilder) {}

    ngOnInit(): void {
        this.createForm();
    }

    createForm() {
        this.form = this.fb.group({
            templateName: [null, [Validators.required]],
            templateContent: [null, [Validators.required]],
            status: [null],
            country: [
                this.requestFinanceTypesOption,
                [
                    (control) => {
                        return control.value.some((item) => item.checked)
                            ? null
                            : { require: true };
                    },
                ],
            ],
        });
    }
}
```

### FormArray

arrayDemo.componemt.html

```html
<nz-spin [nzSpinning]='loading' nzTip='加载中 。。。'>
    <!--这一组formGroup 的名称为name -->
    <form nz-form [formGroup]='form'>
        <div formArrayName='array'>
            <div nz-row nzType='flex' style="margin-top: 6px;" *ngFor="let item of formArray?.comtrols;  let indexItem=index" [formGroupName]= "indexItem">
                <div class='monitor-info'>
                    <nz-form-item class='monitor-info-form-item'>
                        <nz-form-label [nzSpan]='4' nzRequired>监控人</nz-form-label>
                        <nz-form-control [nzSpan]='16'>
                            <!-- 通过 formControlName 来绑定监听input值发生变化-->
                            <nz-select
                                formControlName='monitorUserId'
                                class='gutter-row font12'
                                nzPlaceHolder='监控人'
                                [nzLoading]='loading'
                                nzShowSearch
                            >
                                <nz-option *ngFor="let item of monitorUserOptions" [nzValue]='item?.id' [nzLabel]='item?.text'>
                                </nz-option>
                            <nz-select>
                            <nz-form-explain *ngIf="formArray.controls[indexItem].get('monitorUserId')?.dirty && formArray.controls[indexItem].get('monitorUserId')?.hasError('required')">
                                模版名称必填
                            <nz-form-explain>
                        <nz-form-control>
                    <nz-form-item>

                    <nz-form-item class='monitor-info-form-item'>
                       <nz-form-label [nzSpan]='4' nzRequired>监控人邮箱</nz-form-label>
                       <nz-form-control>
                            <input formControlName='monitorUserEmail' nz-input placeholder='监控人邮箱' />
                            <nz-form-explain *ngIf="formArray.controls[indexItem].get('monitorUserEmail').dirty && formArray.controls[indexItem].get('monitorUserEmail')?.hasError('required')" >
                                请输入监控人邮箱
                            <nz-form-explain>
                            <nz-form-explain *ngIf="formArray.controls[indexItem].get('monitorUserEmail').dirty&& formArray.controls[indexItem].get('monitorUserEmail')?.hasError('email')" >
                                请输入正确的邮箱
                            <nz-form-explain>
                       </nz-form-control>
                    <nz-form-item>

                    <div nz-col nzSpan='1'>
                        <i *ngIf="0 === indexItem" nz-icon nzType='plus-circle' nzTheme='outline' (click)='createRow()' ></i>
                        <i *ngIf="0 !== indexItem" nz-icon nzType='minus-circle' nzTheme='outline' (click)='removalRow(indexItem)'></i>
                    </div>
                </div>
            </div>
        <div>
    </form>
</nz-spin>

```

arrayDemo.componemt.ts

```ts
import { FormArray, FormBuilder, FormGroup, Validators } from "@angular/forms";

export class DemoComponent implements OnInit {
    @Input() checkedItems; // 从父组建传过来的需要回填的数据

    form: FormGroup;

    constructor(private fb: FormBuilder) {}

    ngOnInit(): void {
        this.createForm();
    }

    createForm() {
        let offerMonitorIds = [];

        this.checkedItems.map((item) => {
            offerMonitorIds.push(item.id);
        });

        this.form = this.fb.group({
            array: this.fb.array([]),
            offetMonitorIds: [null],
        });

        this.form.get("offerMonitorIds").setValue(offerMonitorIds); // 回填数据

        this.initRow();
    }

    get formArray() {
        return this.form.get("array") as FormArray;
    }

    createRow() {
        const control = this.fb.group({
            monitorUserId: [null, [Validators.required]],
            monitorUserName: [null],
            monitorUserEmail: [null, [Validators.required, Validators.email]],
        });

        // 监听监控人的变化
        control
            .get("monitorUserId")
            .ValueChanges.pipe(
                takeUntil(this._destroy) //  直到提供的obserVable 发出值，就清除当前状态
            )
            .subscribe((monitorUserId) => {
                const users = this.formArray.value;
                const exitUser = users.find(
                    (val) => +val.monitorUserId === +monitorUserId
                );
                if (exitUser) {
                    this.nzMessageService.error(`监控人不能重复`);
                    control
                        .get("monitorUserId")
                        .setValue(null, { emitEvent: false });
                    return;
                }
            });

        // 调用增加方法
        this.formArray.push(control);
    }

    // 调用删除方法

    removalRow(index: number) {
        const arr = this.formArray;
        arr.removeAt(index);
    }
}
```
