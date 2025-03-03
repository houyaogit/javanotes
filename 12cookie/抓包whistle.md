安装指导： https://www.cnblogs.com/kunmomo/p/11811458.html



控制台：127.0.0.1:8899



​	w2 start , w2 stop



\#---------------------------------------------  sso  ---------------------------------------------



# sso 切换到本地



https://sso.test.mirattery.com/auth http://127.0.0.1:6066/auth



https://sso.test.mirattery.com http://127.0.0.1:6066/auth/sso/ssoLoginScan



https://sso.test.mirattery.com/oauth/callback/feishu http://localhost:8443/oauth/callback/feishu



https://sso.test.mirattery.com/auth/sso/callback http://localhost:8443/oauth/callback/feishu



Baas切换到本地



https://baas.test.mirattery.com/battery/satoken/authList https://baas.test.mirattery.com/battery/satoken/authList



baas 请求本地 sso 权限, 需本地启动 baas & sso , 只开下面一个



https://baas.test.mirattery.com/battery http://127.0.0.1:8989/battery



https://baas.test.mirattery.com http://127.0.0.1:8989



官二账单 下载



\#https://baas.test.mirattery.com/battery/official/query/bill https://baas.test.mirattery.com/battery/official/downloadExcel



订单查询



https://baas.test.mirattery.com/battery/orderInfo/ http://127.0.0.1:8989/battery/orderInfo/



dashboard



https://vision.test.mirattery.com/dashboard/sso/login  http://127.0.0.1:30001/sso/login



https://vision.test.mirattery.com/dashboard/  http://127.0.0.1:30001/



当前用户权限查询



https://sso.test.mirattery.com/auth/userDetailBs/getUserInfo http://127.0.0.1:6066/auth/userDetailBs/getUserInfo



https://baas.test.mirattery.com/battery/rz/plan/getCapitalRzInfo http://127.0.0.1:8989/battery/rz/plan/getCapitalRzInfo



获取本地权限



https://baas.test.mirattery.com/battery/satoken/authList  http://127.0.0.1:8989/battery/satoken/authList



https://baas.test.mirattery.com/battery/satoken/userInfo  http://127.0.0.1:8989/battery/satoken/userInfo



\#https://baas.test.mirattery.com/battery/rz/plan/getOrderRzInfo http://127.0.0.1:8989/battery/rz/plan/getOrderRzInfo
\#https://baas.test.mirattery.com/battery/rz/plan/getOrderRzRecord http://127.0.0.1:8989/battery/rz/plan/getOrderRzRecord
\#https://baas.test.mirattery.com/battery/rz/plan/getOrderRzRecord http://127.0.0.1:8989/battery/rz/plan/getOrderRzRecord



\#https://baas.test.mirattery.com/battery/rz/plan/getCapitalRzInfo http://127.0.0.1:8989/battery/rz/plan/getCapitalRzInfo



增加用户



https://sso.test.mirattery.com/auth/userDetailBs/addUser http://127.0.0.1:6066/auth/userDetailBs/addUser



增加租户， 相同租户 id



https://sso.test.mirattery.com/auth/tenantBs/addTenant http://127.0.0.1:6066/auth/tenantBs/addTenant



增加系统， 相同系统 id



https://sso.test.mirattery.com/auth/sysInfoBs/addSysInfo http://127.0.0.1:6066/auth/sysInfoBs/addSysInfo



https://sso.test.mirattery.com/auth/userRoleBs/addRole http://127.0.0.1:6066/auth/userRoleBs/addRole



https://sso.test.mirattery.com/auth/userRoleBs/updRole http://127.0.0.1:6066/auth/userRoleBs/updRole



https://sso.test.mirattery.com/auth/ http://127.0.0.1:6066/auth/



https://sso.test.mirattery.com/auth/userAuthority/getAuth http://127.0.0.1:6066/auth/userAuthority/getAuth



https://sso.test.mirattery.com/auth/userAuthority/updAuth  http://127.0.0.1:6066/auth/userAuthority/updAuth