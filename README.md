# scanqrcode
使用的前端框架amaze ui
使用了mobiscroll的jquery插件的时间
使用了FileReader来显示图片
使用formdata来提交表单数据

 1. 取消掉之前设计方案，删除status；增加code0，1；表示弹出框选项；0 表示 弹出框带有（是/否）之类的按钮，如果是该类框，会附带着返回一个key为function的键值对，表示接下来可以调用的函数，例子如下所示；1表示一个只读弹出框（带有“是”之类的按钮）
2. 显示的信息由msg决定，前端不再自己拟定msg；8接口updateAppStatus，不需要传入任何status之类的参数，传入uid lotId即可，后台根据实际情况自己更改（详见8）
3. endTime未必是一个有用的选项，但startTime肯定是下一次预约的时间；如果是普通用户，只能在现场扫码后的半小时内预约停车，后台已做相关处理，返回的startTime已经是普通用户的最晚截截止时间；
4. 设置了一定的缓冲区15min，15min内的空隙段不能预约停车，但反过来提前到的当前预约用户可以提前停车（如果有bug暂时未知）

[request]:
fname: parkingData
fparam: 
残疾人
{
      "uid": "912a6a6e-3295-11e8-a262-00163e104758",
      "lotId": "d491c006-2681-11e8-ae1d-00163e104758"
}
普通用户
{
      "uid": "877c212e-3295-11e8-a262-00163e104758",
      "lotId": "d491c006-2681-11e8-ae1d-00163e104758"
}
[response]:
--2018/4/4
（注：当前时间后15分钟内有预约，则别人也不可以预约这小于15分钟的空隙停车，例如现在23：18，23：33有别人预约了，则仍返回（）；反之，如果是本人在提前15分钟内到了，则可以提前停车（））
--车位当前没有存在预约时 
残疾用户：
a
{
    "success": 1,
    "result": {
        "msg": "当前车位未被预约，是否预约?(残疾用户)",
        "function": "reserveParkingLotById",（传回函数名意思是可调用该函数）
        "startTime": "2018-04-05 00:00",(当前时间后没有任何预约，返回下一天0点)
        "endTime": "2018-04-05 00:00",
        "code": 0
    }
}
普通用户：
b
{
    "success": 1,
    "result": {
        "msg": "当前车位未被预约，是否预约?(普通用户)",
        "function": "reserveParkingLotById",
        "startTime": "2018-04-04 23:39",（普通用户只能预约最多半小时，因此时间为curentTime半小时后，测试时间为23：09）
        "endTime": "2018-04-05 00:00",
        "code": 0
    }
}

调用reserveParkingLotById预约后
本人扫码：
第一次扫码，提示msg，可调用updateAppStatus（uid，lotId）
{
    "success": 1,
    "result": {
        "msg": "本人扫码，是否现在停车？",
        "function": "updateAppStatus",
        "startTime": "2018-04-04 23:32",
        "endTime": "2018-04-05 01:10",
        "code": 0
    }
}

若停车后，再次扫码，可调用updateAppStatus（uid，lotId）结束使用
{
    "success": 1,
    "result": {
        "msg": "本人扫码，是否现在结束使用？",
        "function": "updateAppStatus",
        "startTime": "2018-04-04 23:32",
        "endTime": "2018-04-05 01:10",
        "code": 0
    }
}

非本人扫码：
有人停车
{
    "success": 1,
    "result": {
        "msg": "非预约者本人，当前车位被预约或使用中",
        "startTime": "2018-04-04 23:32",
        "endTime": "2018-04-05 01:10",
        "code": 1
    }
}

车位20分钟内空着没来
{
    "success": 1,
    "result": {
        "msg": "非预约者本人，当前车位被预约，用户已超时，是否举报？",
        "function": "reportViolation",
        "startTime": "2018-04-04 22:28",
        "endTime": "2018-04-04 23:45",
        "code": 0
    }
}

select registerUser('{"usr_id":111}');
直接传入用户手机号（varchar型），将获取用户的uuid
