---
title: iOS控件-UITextfield
date: 2016-03-06 15:47:53
tags:
- iOS
categories:
- iOS
---

本文，主要记录一点开发中的实战。

- TextField输入限制
- TextField输入格式化
- 键盘遮挡输入框

<!--more-->

## 首先，UITextfield 代理

```objective-c
@protocol UITextFieldDelegate <NSObject>

@optional

- (BOOL)textFieldShouldBeginEditing:(UITextField *)textField;        // return NO to disallow editing.
- (void)textFieldDidBeginEditing:(UITextField *)textField;           // became first responder
- (BOOL)textFieldShouldEndEditing:(UITextField *)textField;          // return YES to allow editing to stop and to resign first responder status. NO to disallow the editing session to end
- (void)textFieldDidEndEditing:(UITextField *)textField;             // may be called if forced even if shouldEndEditing returns NO (e.g. view removed from window) or endEditing:YES called
- (void)textFieldDidEndEditing:(UITextField *)textField reason:(UITextFieldDidEndEditingReason)reason NS_AVAILABLE_IOS(10_0); // if implemented, called in place of textFieldDidEndEditing:

- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string;   // return NO to not change text

- (BOOL)textFieldShouldClear:(UITextField *)textField;               // called when clear button pressed. return NO to ignore (no notifications)
- (BOOL)textFieldShouldReturn:(UITextField *)textField;              // called when 'return' key pressed. return NO to ignore.

@end
```

## TextField输入限制

在`TextFieldDelegate`中设置，在`textField:shouldChangeCharactersInRange:replacementString`中，通过:

```objective-c
textField.text.length - range.length + string.length > maxlength;
```

来进行判断,是否超过最大长度。

一些输入判断的代码片段如下：

```objective-c

// 包含分机号的验证
- (BOOL)isCompanyPhone:(NSString *)telphone {
    NSString *phoneRegex = @"^(0\\d{2,3}-\\d{7,8}(-\\d{3,5}){0,1})|(((13[0-9])|(15([0-3]|[5-9]))|(17[0,0-9])|(18[0,5-9]))\\d{8})|^(0\\d{2,3}\\d{7,8}(\\d{3,5}){0,1})|(((13[0-9])|(15([0-3]|[5-9]))|(17[0,0-9])|(18[0,5-9]))\\d{8})$";
    NSPredicate *phoneTest = [NSPredicate predicateWithFormat:@"SELF MATCHES %@",phoneRegex];
    return [phoneTest evaluateWithObject:telphone];
}
// 固话验证
- (BOOL)validateTelphone:(NSString *)telphone {
    NSString *phoneRegex = @"\\d{3}-\\d{8}|\\d{4}-\\d{7,8}";
    NSPredicate *phoneTest = [NSPredicate predicateWithFormat:@"SELF MATCHES %@",phoneRegex];
    return [phoneTest evaluateWithObject:telphone];
}
// 手机号验证
- (BOOL)phoneNumber:(NSString *)phoneNumber {
    NSString *phoneNumberRegularExpression = @"((13[0-9])|(14[0-9])|(15[0-9])|(18[0,0-9])|(17[0,0-9]))\\d{8}";
    BOOL isPhoneNumber = [[NSPredicate predicateWithFormat:@"SELF MATCHES %@",phoneNumberRegularExpression]evaluateWithObject:phoneNumber];;
    //    ((13[0-9])|(15[0-9])|(18[0-9]|(17[0-9]))\\d{8}
    return isPhoneNumber;
}
// 银行卡开户行验证
- (BOOL)isBankTextField:(NSString *)bankCard {
    NSString *pattern = @"[a-zA-Z\u4e00-\u9fa5][a-zA-Z0-9\u4e00-\u9fa5]+";
    NSPredicate *pred = [NSPredicate predicateWithFormat:@"SELF MATCHES %@", pattern];
    BOOL isMatch = [pred evaluateWithObject:bankCard];
    return isMatch;
}

// 邮箱验证
- (BOOL)isEmailTextField:(NSString *)email {
    NSString *pattern = @"^[a-z0-9]+([._\\-]*[a-z0-9])*@([a-z0-9]+[-a-z0-9]*[a-z0-9]+.){1,63}[a-z0-9]+$";
    NSPredicate *pred = [NSPredicate predicateWithFormat:@"SELF MATCHES %@", pattern];
    BOOL isMatch = [pred evaluateWithObject:email];
    return isMatch;
}

// 身份证号码验证
- (BOOL)IDTextField:(NSString *)ids {
    NSString *mmdd = @"(((0[13578]|1[02])(0[1-9]|[12][0-9]|3[01]))|((0[469]|11)(0[1-9]|[12][0-9]|30))|(02(0[1-9]|[1][0-9]|2[0-8])))";
    NSString *leapMmdd = @"0229";
    NSString *year = @"(19|20)[0-9]{2}";
    NSString *leapYear = @"(19|20)(0[48]|[2468][048]|[13579][26])";
    NSString *yearMmdd = [NSString stringWithFormat:@"%@%@", year, mmdd];
    NSString *leapyearMmdd = [NSString stringWithFormat:@"%@%@", leapYear, leapMmdd];
    NSString *yyyyMmdd = [NSString stringWithFormat:@"((%@)|(%@)|(%@))", yearMmdd, leapyearMmdd, @"20000229"];
    NSString *area = @"(1[1-5]|2[1-3]|3[1-7]|4[1-6]|5[0-4]|6[1-5]|82|[7-9]1)[0-9]{4}";
    NSString *regex = [NSString stringWithFormat:@"%@%@%@", area,yyyyMmdd,@"[0-9]{3}[0-9Xx]"];
    BOOL isID = [[NSPredicate predicateWithFormat:@"SELF MATCHES %@",regex]evaluateWithObject:ids];
    return isID;
}

// 英文
- (BOOL)isLetter:(NSString *)ids{
    NSString *IDRegularExpression = @"^[A-Za-z]+$";
    BOOL isID = [[NSPredicate predicateWithFormat:@"SELF MATCHES %@",IDRegularExpression]evaluateWithObject:ids];
    return isID;
}

// 数字
- (BOOL)isNumber:(NSString *)ids {
    NSString *IDRegularExpression = @"^[0-9]*$";
    BOOL isID = [[NSPredicate predicateWithFormat:@"SELF MATCHES %@",IDRegularExpression]evaluateWithObject:ids];
    return isID;
}
// 汉字
-(BOOL)isChinese:(NSString *)ids {
    NSString *IDRegularExpression = @"^[\u4e00-\u9fa5]{0,}$";
    BOOL isID = [[NSPredicate predicateWithFormat:@"SELF MATCHES %@",IDRegularExpression]evaluateWithObject:ids];
    return isID;
}

-(BOOL)IsChinese:(NSString *)str {
    for(int i=0; i< [str length];i++){
        int a = [str characterAtIndex:i];
        if( a > 0x4e00 && a < 0x9fff){
            return YES;
        }
    }
    return NO;
}

// 姓名 判断 只有汉子 和 .
- (BOOL)isNameTextField:(NSString *)ids {
    BOOL isOk = NO;
    // 位数判断
    if(ids.length<2||ids.length>10){
        isOk = YES;
    }
    
    // 不会写汉字＋ . 的正则 只能用判断每个字符的正则
    for (int i=0; i<[ids length]; i++) {
        int a = [ids characterAtIndex:i];
        
        if ( a > 0x4e00 && a < 0x9fff ) {
            
        } else {
            if (a != 8226 && a != 19968&&a != 183) {
                isOk = YES;
            }
        }
    }
    return isOk;
}
// 2~10字，除汉字、字母、数字外其他字符不可以
- (BOOL)isMaxTen:(NSString *)ids {
    BOOL isOk = NO;
    //位数判断
    if(ids.length<2||ids.length>10){
        isOk = YES;
    }
    
    //不会写汉字＋ . 的正则 只能用判断每个字符的正则
    
    for (int i=0; i<[ids length]; i++) {
        
        int a = [ids characterAtIndex:i];
        
        if ( a > 0x4e00 && a < 0x9fff ) {//中文
            
        }else if (a > 47 && a < 58){//数字
            
        }else if (a > 64 && a < 91){//大写字母
            
        }else if (a > 96 && a < 123){//小写字母
            
        }else{
            if (a != 19968) {
                isOk = YES;
            }
            
        }
    }
    return isOk;
}

// 6~20个字母、数字、下划线或减号
- (BOOL)isWexin:(NSString *)ids {
    BOOL isOk = NO;
    //位数判断
    if(ids.length<6||ids.length>20){
        isOk = YES;
    }
    
    //不会写汉字＋ . 的正则 只能用判断每个字符的正则
    
    for (int i=0; i<[ids length]; i++) {
        
        int a = [ids characterAtIndex:i];
        
        if (a > 47 && a < 58){//数字
            
        }else if (a > 64 && a < 91){//大写字母
            
        }else if (a > 96 && a < 123){//小写字母
            
        }else{
            
            if(a!=65343&&a!=65293&&a!=95&&a!=45&&a != 19968){//65343下划线65293减号
                isOk = YES;
            }
        }
    }
    return isOk;
}

```



## TextField输入格式化

输入需要进行格式化处理，使输入内容按照一定格式以空格进行分隔。因为这种格式化会导致光标的异常，处理光标是一件比较麻烦的事。

输入控制在这个回调中进行控制时，对于选中一块进行输入或删除时，会出现光标异常的情况，处理方法是，对于所有`textField:shouldChangeCharactersInRange:replacementString`直接全部返回`NO`，正是因为返回的`YES`导致了`textfield`自行删除添加，然后导致光标移动，而如果返回`NO`，首先光标不会乱动，然后在设置光标的位置，使光标显示正常。而由于返回了`NO`导致无法进行自动输入或删除内容到`textField`，要使用`textfield`的`UIKeyInput`中的`insertText:`和`deleteBackward`方法来进行内容的输入与删除。



## 键盘遮挡输入框

通过在`ViewController`注册键盘弹出的通知，分别在键盘显示和键盘隐藏的时候对视图做一个动画处理，滚动到一个**偏移量**用户可视范围内。基于这个思路，我们需要知道四个重要的数值去计算，**偏移量**。

- 键盘高度
- 获取焦点视图的位置和高度
- 屏幕的高度

```objective-c
// 注册键盘出现的通知
    [[NSNotificationCenter defaultCenter] addObserver:self
                                             selector:@selector(keyboardWillShow:)
                                                 name:UIKeyboardWillShowNotification object:nil];
    // 注册键盘消失的通知
    [[NSNotificationCenter defaultCenter] addObserver:self
                                             selector:@selector(keyboardWillHide:)
                                                 name:UIKeyboardWillHideNotification object:nil];

```

