<template>
    <div class="login_container">
        <div class="login_box">
            <div class="avatar_box">
                <img src="../assets/logo.png" alt="">
            </div>
            <!-- 登陆表单区域 -->
            <div>
                <el-form ref="loginFormRef" :model="loginForm" :rules="loginFormRules" label-width="0px" class="login_form">
                    <!--用户名-->
                    <el-form-item prop="username">
                        <el-input v-model="loginForm.username" prefix-icon="el-icon-user-solid"></el-input>
                    </el-form-item>
                    <!--密码 -->
                    <el-form-item prop="password">
                        <el-input type="password" v-model="loginForm.password" prefix-icon="el-icon-lock"></el-input>
                    </el-form-item>
                    <el-form-item class="btns">
                        <el-button type="primary" @click="login">登陆</el-button>
                        <el-button type="info" @click="resetLoginForm">重置</el-button>
                    </el-form-item>
                </el-form>
            </div>
        </div>
    </div>

</template>

<script>
    export default {
        name: "Login",
        data(){
            return {
                //这是登陆表单的数据绑定对象
                loginForm:{
                    username:'admin',
                    password:'password'

                },
                //这是表单的验证规则对象
                loginFormRules:{
                    username:[
                        {required:true,message:"请输入用户名",trigger:"blur"},
                        {min:3,max:10,message: "长度在3-10个字符之间",trigger: "blur"}
                        ],
                    password:[
                        {required:true,message:"请输入密码",trigger:"blur"},
                        {min:6,max:15,message: "长度在5-15个字符之间",trigger: "blur"}
                        ]

                }
            }

        },
        methods:{
            //点击重置按钮重置表单
            resetLoginForm(){
                // console.log(this);
                this.$refs.loginFormRef.resetFields();

            },
            login(){
                this.$refs.loginFormRef.validate(async valid=>{
                    console.log(valid);
                    if(!valid) return;
                    // let params = new FormData();
                    // params.append('username',this.loginForm.username);
                    // params.append('password',this.loginForm.password);

                    let params = new URLSearchParams();
                    params.append('username', this.loginForm.username);
                    params.append('password', this.loginForm.password);
                    const {data : res} = await this.$http.post('api/login',params,{headers: {'Content-Type':'application/x-www-form-urlencoded'}});
                    console.log(res);
                    if(res.statusCodeValue !== 200) return this.$message.error('登陆失败')
                    this.$message.success('登陆成功')
                    //1.将登陆成功之后的token，保存到客户端的sessionStorage中
                    //  1.1 项目中除了登陆之外的其他api接口，必须在登陆之后才能访问
                    //  1.2 token只应在当前网站打开期间生效，所以将 token 保存在 sessionStorage中
                    window.sessionStorage.setItem('user',res.body);
                    //2.通过编程式导航跳转到后台主页，路由地址式/home
                    this.$router.push('/home');
                });
            }
        }
    }
</script>

<style lang="less" scoped>
    .login_container{
        background-color: #2b4b6b;
        height: 100%;
    }
    .login_box{
        width: 450px;
        height: 300px;
        background-color: #fff;
        border-radius: 3px;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%,-50%);

        .avatar_box{
            height: 130px;
            width: 130px;
            border:1px solid #eee;
            border-radius: 50%;
            padding: 10px;
            box-shadow: 0 0 10px #ddd;
            position: absolute;
            left: 50%;
            transform: translate(-50%,-50%);
            background-color: #fff;
            img{
                width: 100%;
                height: 100%;
                border-radius: 50%;
                background-color: #eee;
            }
        }
    }
    .btns{
        display: flex;
        justify-content: flex-end;
    }
    .login_form{
        position: absolute;
        bottom: 0;
        width: 100%;
        padding: 0 20px ;
        box-sizing: border-box;
    }
</style>