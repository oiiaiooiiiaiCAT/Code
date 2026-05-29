欧鸥 - git bash推送方法：
第一种：用vscode（我喜欢）
1.点击fork把别人仓库复制到自己的github账号
2.在bash中输入
git clone git@github.com:我的用户名/仓库名.git  （ssh方式）
然后就能克隆自己的仓库了
3.在文件管理中找到自己的code文件，用vscode打开
4.编辑、写代码、改文档
5.结束后点资源管理器，勾选暂存更改并提交即可

第二种：
1.点击fork把别人仓库复制到自己的github账号
2.在bash中输入
git clone git@github.com:我的用户名/仓库名.git  （ssh方式）
3.在bash中输入
cd 仓库名
4.修改文件保存
5.git add .(一次性暂存所有修改过的文件)
6.git commit -m "备注说明"
7.git push origin main

欧鸥 - 云台逻辑：
拨动摇杆(CH2-YAW)
        │
        ▼
遥控器通过 SBUS 协议连接到 STM32 的 USART3 串口──→ sbus_to_rc() 解析出 ch[2] 的值
        │
        ▼
gimbal_behavour_set() 判断开关位置 → GIMBAL_RELATIVE_ANGLE手动操控模式
        │
        ▼
gimbal_relative_angle_control(): 
    *yaw = ch[2] × YAW_RC_SEN - mouse.x × YAW_MOUSE_SEN
        │
        ▼
gimbal_set_control(): 调用 gimbal_relative_angle_limit()
    把增量加到 relative_angle_set 上，并限幅防止超行程
        │
        ▼
gimbal_control_loop(): 选择 ENCONDE 模式
        │
        ▼
gimbal_motor_relative_angle_control():
    角度环PID → 速度环PID → given_current (电流值)  双环PID
        │
        ▼
CAN_cmd_gimbal(): 电流值打包成CAN帧，发给6020电机
        │
        ▼
    电机转动 → 编码器角度变化 → gimbal_feedback_update() 更新 relative_angle
        │
        ▼
    PID闭环: relative_angle 追上 relative_angle_set → 云台转到目标角度