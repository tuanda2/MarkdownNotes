
[TOC]

###**VPL Mobile App - Hướng dẫn gọi service SMS, OTP & Email**

####**1. Add Project reference thư viện SMS & OTP**
> * Add dll reference:
 * SMS dll:   `ADR.SMSCenter.VinHome.Compliled.dll`
 * OTP dll: `VinEcom.OTP.PublicClient.dll`

####**2. Add vào Project các Class**
>* Add class handle việc send SMS OTP: `SmsOtpPartner.cs`
>* Add class handle việc Send Email: `MailClient.cs`

####**3. Cấu hình `Web.config`**
#####**3.1 Cấu hình SMS OTP Service**
```html
<appSettings>

    <!--key sms adayroi dev-->
    <add key="ADR.SMSCenter.VinHomes.AppID" value="0ca726f111491f8e050da0234b2f7027" />
    <add key="ADR.SMSCenter.VinHomes.ConsumerSecret" value="6F2CE50B933111B30D9FFA37569E9A3B" />
    <add key="ADR.SMSCenter.VinHomes.RSAPublickey" value="PFJTQUtleVZhbHVlPjxNb2R1bHVzPnNYY3RvTW9seU1LZkVISDRFWlhJeHlIb2NkZEFwamg5bTZMMlhicjJLSjJ1eTFJTGJNTmp3allWbkM3MTFqN2F2aGhEdmljNTZBMU82bjRrNXpzU1MxdzVNcDl5dURwR2R2V0Fnd1RkYlBjYUVUcVU3czVQVXRLK2VPMDVWbEE4QTRwNDNVMk9BSnlGa3pvb3NCM3gxbWdQQmJZVFlXR0lBK3VBU2xtR1l6TT08L01vZHVsdXM+PEV4cG9uZW50PkFRQUI8L0V4cG9uZW50PjwvUlNBS2V5VmFsdWU+" />
    <add key="ADR.SMSCenter.VinHomes.Uri" value="http://api.sms.adayroi.dev/" />
    <!--end key sms adayroi dev-->
    
    <!--key otp adayroi dev-->
    <add key="OTPAppid" value="846625bd2eae52dfefb428b78dcef545" />
    <add key="OTPConsumerSecret" value="0111621E20D98E829E3BE0483CF7CCF8" />
    <add key="OTPPublickey" value="PFJTQUtleVZhbHVlPjxNb2R1bHVzPnZpSHZqdHIxbDRESGV5R2t2L0ZCVGRnSWlHZ2ZheUxOS0o5QkMvSGxUT3YyN21Ic2RYazdDNmRuWW5EWnl0eHJJREdKSjVxQ25kUjMwdGlPY3RMM0psNEsvQlg2b25VeWVxTmgwd1lsWjhPbTlDWDRSdndjM2M0UWtvRzl2TGRnTXRtQTdRRUdEYjhrYy91UTBnZDZqWkwyc29aenpseXAzRitwZEFrTUZxRT08L01vZHVsdXM+PEV4cG9uZW50PkFRQUI8L0V4cG9uZW50PjwvUlNBS2V5VmFsdWU+" />
    <add key="OTPAPI_Uri" value="http://api.otp.adayroi.dev/" />
    <add key="StatusOtp" value="False" />
    <!--key otp adayroi dev-->

    <!--app key vinhome authen-->
    <add key="app_key" value="PFJTQUtleVZhbHVlPjxNb2R1bHVzPnVCRXM1UUY2akpzSGdCRmdKaERJSjZaVEIwK1VKdC80aHYwYmhtNHJ2amlsdUtqaWFWOVlDY1M2TEkwdmZVczhwSzZ1UWxGanplaGdkUGpDa01kaUFmaDNZODlVejNTaGpiV1FEbXNLZ0xNK0VHckJhdEN0YUQ0MERPUlcvaTRrd1JrLzU4cFhrZWprdndEVFJkd0xrQU9OOXBNNXF2QVZPVlJpMkczdVB6cz08L01vZHVsdXM+PEV4cG9uZW50PkFRQUI8L0V4cG9uZW50PjwvUlNBS2V5VmFsdWU+" />
    <!--end app key vinhome authen-->
</appSettings>
```
#####**3.2 Cấu hình Email Service**
```html
 <!-- Config mail server-->
    <add key="MailServerDomainSmtp" value="ex.vingroup.net"/>
    <add key="MailServerDomainSmtpPort" value="25"/>
    <add key="MailSmtpUserName" value="mail_user"/>
    <add key="MailSmtpPassword" value="mail_password"/>
    <add key="MailSender" value="noreply@vingroupcard.net"/>
    <!--<add key="DomainVerifyEmail" value="https://apilayer.net/api/check?access_key=faf7a44949ffc3f612effc9d4e853f43" />-->
    <add key="DomainVerifyEmail" value="http://dev-validate-email.vingroup.net/quickemailverification/v1/verify"/>
    <add key="QuickEmailVerificationApiKey" value="66f197466f6f97a1a42254fdb4297017933dd8ffbd1335fbdcc85afb703d"/>
    <!-- End config mail server-->
```
***Giải thích:***
>* Key : **`DomainVerifyEmail`** để gọi đến dịch vụ xác thực Email (check Mail box có tồn tại hay không?) 
>* Key : **`QuickEmailVerificationApiKey`** Client key để gọi đến dịch vụ xác thực Email (check Mail box có tồn tại hay không?) 



####**4. Using Code**
#####**4.1 OTP Service**
* *Generate OTP*
```c#
{
/*
* secretkey: để định danh Client khi xác thực OTP
* Mỗi số Mobile sẽ tương ứng với 1 secretkey
*/
secretkey = SmsOtpPartner.GenerateKey(model.Phone);
                                optCode = 
// sinh mã OTP
SmsOtpPartner.GenerateOTPCode(secretkey);
}
```
 * *Verify OTP*
```c#
{
/* validOtp: kết quả Validate OTP 
* <returns>bool</returns>
*/
// Lấy số thông tin secretkey đã gen cho số Mobile (oldToken.SecretkeyOtp)
var oldToken = DataAccess.GetOldToken(new object[] { model.Phone });
validOtp = SmsOtpPartner.ValidOTPCode(oldToken.SecretkeyOtp, model.Otp);
                    }
```
#####**4.2 SMS Service**
* *Send SMS (số OTP)*
```c#
{
// smsStatus: kết quả Validate OTP 
///<returns>bool</returns>
smsStatus = SmsOtpPartner.SendSmsOtpToAdayroi(model.Phone, optCode);
}
```
#####**4.3 Email Service**
* *Verify Email:* check Mailbox tồn tại trước khi Send Email (apply cho case khi đăng ký khi User nhập Email, Mobile rồi submit form. Hệ thống sẽ check Email đăng ký)
```c#
{
// isValidEmai: kết quả check Mailbox tồn tại hay không 
///<returns>bool</returns>
MailClient mailClient = new MailClient();
var isValidEmai = mailClient.isEmail(model.Email)
}
```

* *Send Email:* Tham số tham khảo Class ```MailModel```
```c#
private MailClient mailServer = new MailClient();
...
{
this.mailModel = new MailModel();
                        this.mailModel.Subject = EmailResource.ActiveEmail_Subject;
                        this.mailModel.Token = new List<Token>() { new Token() { TokenName = "Token0", Value = this.Url }};

                        this.mailModel.To = data.Email;
                        //this.mailModel.To = "v.nghiepnc@vincommerce.com";
                        this.mailModel.TemplatePath = EmailResource.ActiveEmail_PathFile;
                        this.mailModel.HtmlBody = this.mailServer.GeneralTemplate(this.mailModel.TemplatePath, this.mailModel.Token);

// Send Email theo các thông tin tham khảo từ                 this.mailServer.SendMailSMTP(this.mailModel);
}
```

```c#
public class MailModel
{
    public string From { get; set; }
    public string To { get; set; }
    public IList<string> Cc { get; set; }
    public IList<string> Bcc { get; set; }
    public string Subject { get; set; }
    public string HtmlBody { get; set; }
    public List<Token> Token { get; set; }
    public string TemplatePath { get; set; }
}
```