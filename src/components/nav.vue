<template>
    <el-header class="fater-header">
        <div class="fater-header-logo">
            <el-row :gutter="16">
                <el-col :span="6">
                    <img
                        src="@/assets/04.png"
                        style="
                            width: 35px;
                            height: 35px;
                            margin: 17px 5px 0 10px;
                            -webkit-user-drag: none;
                            -khtml-user-drag: none;
                            -moz-user-drag: none;
                            user-drag: none;
                        "
                    />
                </el-col>
                <el-col :span="18" style="font-size: 26px">
                    社团管理系统
                </el-col>
            </el-row>
        </div>
        <el-dropdown @command="handleUser" class="fater-header-user">
            <span class="el-dropdown-link">
                <el-row>
                    <el-col :span="7">
                        <el-avatar
                            shape="square"
                            :size="55"
                            :src="require('@/assets/avatar.jpg')"
                            style="
                                margin: 5px 40px 0 0;
                                border: 1px solid lightgrey;
                            "
                        ></el-avatar>
                    </el-col>
                    <el-col :span="17" style="font-size: 24px; font-weight: 600;text-align: center;">
						{{$store.state.user != undefined ? $store.state.user.name : ''}}
					</el-col>
                </el-row>
            </span>
            <el-dropdown-menu slot="dropdown">
                <el-dropdown-item command="info">修改信息</el-dropdown-item>
                <el-dropdown-item command="pwd">修改密码</el-dropdown-item>
                <el-dropdown-item command="exit">退出系统</el-dropdown-item>
            </el-dropdown-menu>
        </el-dropdown>

        <el-dialog
            title="修改信息"
            width="700px"
            :append-to-body="true"
            :visible.sync="showUpdInfoFlag"
        >
            <el-form label-width="80px" :model="userForm">
				<el-form-item label="用户头像">
				
				  <el-upload
				
				    class="avatar-uploader"
				
				    action="/api/upload/avatar"
				
				    :headers="{ authorization: 'Bearer ' + this.$store.state.token }"
				
				    :show-file-list="false"
				
				    :on-success="handleAvatarSuccess"
				
				  >
				
				    <img v-if="userForm.avatar" :src="userForm.avatar" class="avatar">
				
				    <i v-else class="el-icon-plus avatar-uploader-icon"></i>
				
				  </el-upload>
				
				</el-form-item>
                <el-row :gutter="15">
                    <el-col :span="12">
                        <el-form-item label="用户账号">
                            <el-input
                                v-model="userForm.userName"
                                placeholder="请输入用户账号…"
                                autocomplete="off"
                            ></el-input>
                        </el-form-item>
                    </el-col>
                    <el-col :span="12">
                        <el-form-item label="用户密码">
                            <el-input
                                v-model="userForm.passWord"
                                type="password"
                                placeholder="请输入用户密码…"
                                autocomplete="off"
                            ></el-input>
                        </el-form-item>
                    </el-col>
                </el-row>
                <el-row :gutter="15">
                    <el-col :span="12">
                        <el-form-item label="用户姓名">
                            <el-input
                                v-model="userForm.name"
                                placeholder="请输入用户姓名…"
                                autocomplete="off"
                            ></el-input>
                        </el-form-item>
                    </el-col>
                    <el-col :span="12">
                        <el-form-item label="用户年龄">
                            <el-input
                                v-model="userForm.age"
                                placeholder="请输入用户年龄…"
                                autocomplete="off"
                            ></el-input>
                        </el-form-item>
                    </el-col>
                </el-row>
                <el-row :gutter="15">
                    <el-col :span="12">
                        <el-form-item label="用户性别">
                            <el-radio-group v-model="userForm.gender">
                                <el-radio label="男"></el-radio>
                                <el-radio label="女"></el-radio>
                            </el-radio-group>
                        </el-form-item>
                    </el-col>
                    <el-col :span="12">
                        <el-form-item label="联系电话">
                            <el-input
                                v-model="userForm.phone"
                                placeholder="请输入联系电话…"
                                autocomplete="off"
                            ></el-input>
                        </el-form-item>
                    </el-col>
                </el-row>
                <el-form-item label="联系地址">
                    <el-input
                        rows="4"
                        type="textarea"
                        v-model="userForm.address"
                        placeholder="请输入联系地址…"
                        autocomplete="off"
                    ></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="showUpdInfoFlag = false" style="font-size: 18px"> 取 消</el-button>
                <el-button type="primary" @click="updInfo()" style="font-size: 18px"> 确 定</el-button>
            </div>
        </el-dialog>

        <el-dialog
            title="修改密码"
            width="600px"
            :append-to-body="true"
            :visible.sync="showUpdPwdFlag"
        >
            <el-form
                label-width="80px"
                ref="userPwd"
                :model="userPwd"
                :rules="userPwdFormRules"
            >
                <el-form-item label="原始密码" prop="oldPwd">
                    <el-input
                        type="password"
                        v-model="userPwd.oldPwd"
                        placeholder="请输入原始密码……"
                        autocomplete="off"
                    >
                    </el-input>
                </el-form-item>
                <el-form-item label="修改密码" prop="newPwd">
                    <el-input
                        type="password"
                        v-model="userPwd.newPwd"
                        placeholder="请输入修改密码……"
                        autocomplete="off"
                    >
                    </el-input>
                </el-form-item>
                <el-form-item label="确认密码" prop="rePwd">
                    <el-input
                        type="password"
                        v-model="userPwd.rePwd"
                        placeholder="请再次确认密码……"
                        autocomplete="off"
                    >
                    </el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="showUpdPwdFlag = false" 
                                style="font-size: 18px">  取 消</el-button>
                <el-button type="primary" @click="updPwd('userPwd')"
                
                                style="font-size: 18px"
                    >  确 定</el-button
                >
            </div>
        </el-dialog>
    </el-header>
</template>

<style>
</style>

<script>
import {
    exit,
    getLoginUser,
    checkUserPwd,
    updLoginUserInfo,
    updLoginUserPwd,
} from "../api";


export default {
    data() {
        var checkOldPwd = async (rule, value, callback) => {
            if (value) {
                await checkUserPwd(this.$store.state.token, value).then(
                    (resp) => {
                        if (resp.code != 0) {
                            callback(new Error("原始密码输入错误"));
                        }
                    }
                );
            } else {
                callback(new Error("原始密码必须输入"));
            }
            callback();
        };
        var checkNewPwd = (rule, value, callback) => {
            if (!value) {
                callback(new Error("修改密码必须输入"));
            }

            callback();
        };
        var checkRePwd = (rule, value, callback) => {
            if (!value) {
                callback(new Error("确认密码必须输入"));
            }

            if (value != this.userPwd.newPwd) {
                callback(new Error("两次输入密码不一致"));
            }

            callback();
        };
        return {
            showUpdInfoFlag: false,
            showUpdPwdFlag: false,
            userPwd: {
                oldPwd: "",
                newPwd: "",
                rePwd: "",
            },
            userPwdFormRules: {
                oldPwd: [
                    {
                        validator: checkOldPwd,
                        trigger: "blur",
                    },
                ],
                newPwd: [
                    {
                        validator: checkNewPwd,
                        trigger: "blur",
                    },
                ],
                rePwd: [
                    {
                        validator: checkRePwd,
                        trigger: "blur",
                    },
                ],
            },
            userForm: {
                id: "",
                userName: "",
                passWord: "",
                name: "",
                gender: "",
                age: "",
                phone: "",
                address: "",
				avatar: "",
            },
        };
    },
	mounted() {

        setTimeout(() => {

            
        }, 100)
	},
    methods: {
		handleAvatarSuccess(response, file) {
		
		    if (response.code === 0 && response.data && response.data.avatarUrl) {
		
		      this.userForm.avatar = response.data.avatarUrl;
		
		    } else {
		
		      this.$message.error('上传头像失败');
		
		    }
		
		  },
        handleUser(comm) {
            if (comm == "info") {
                this.showUpdInfoWin();
            }

            if (comm == "pwd") {
                this.showUpdPwdWin();
            }

            if (comm == "exit") {
                this.$confirm("确认要退出吗？", "系统提示", {
                    confirmButtonText: "确认",
                    cancelButtonText: "取消",
                    type: "warning",
                }).then(() => {
                    exit(this.$store.state.token).then(() => {
                        this.$store.commit("clearToken");
                        this.$store.commit("clearMenus");
                        sessionStorage.clear();
                        this.$router.push("/");
                    });
                });
            }
        },
        initUserPwd() {
            this.userPwd = {
                oldPwd: "",
                newPwd: "",
                rePwd: "",
            };
        },
        initUserForm() {
            this.userForm = {
                id: "",
                userName: "",
            };
        },
        showUpdInfoWin() {
            getLoginUser(this.$store.state.token).then((resp) => {
                this.initUserForm();
                this.userForm = resp.data;
                this.showUpdInfoFlag = true;
            });
        },
        showUpdPwdWin() {
            this.initUserPwd();
            this.showUpdPwdFlag = true;
        },
        updInfo() {
            this.userForm.token = this.$store.state.token;
            updLoginUserInfo(this.userForm).then((resp) => {
                this.$message({
                    message: resp.msg,
                    type: "success",
                });

                this.showUpdInfoFlag = false;
                this.initUserForm();
            });
        },
        updPwd(formName) {
            this.$refs[formName].validate((valid) => {
                if (valid) {
                    updLoginUserPwd(
                        this.$store.state.token,
                        this.userPwd.newPwd
                    ).then((resp) => {
                        this.$message({
                            message: resp.msg,
                            type: "success",
                        });

                        this.showUpdPwdFlag = false;
                        this.initUserPwd();
                    });
                } else {
                    return false;
                }
            });
        },
    },
};
</script>
