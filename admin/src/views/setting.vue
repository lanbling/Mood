<template>
    <div v-loading.fullscreen.lock="fullscreenLoading">
        <div class="header">
            <h2 class="tit">网站信息</h2>
        </div>
        <el-form ref="form" :model="form" label-width="100px">

            <template v-for="(item, index) in formList[0]">

                <el-form-item :label="item.value" v-if="!item.show" :key="index">
                    
                    <template v-if="item.key == 'avatar'">
                        <el-upload
                            action=""
                            class="avatar-uploader"
                            :auto-upload="false"
                            :show-file-list="false"
                            :on-change="avatarUpload"
                        >
                            <img v-if="avatar['url'] || form.avatar" :src="avatar['url'] || form.avatar">
                            <i v-else class="el-icon-user"></i>
                        </el-upload>
                    </template>

                    <template v-else-if="item.key == 'upload_type'">
                        <el-radio-group v-model="form.upload_type">
                            <el-radio label="服务器"></el-radio>
                            <el-radio label="阿里云OSS"></el-radio>
                        </el-radio-group>
                        <template v-if="form.upload_type == '阿里云OSS'">
                            <template v-for="(item, index) in formList[2]">
                                <el-input 
                                    v-model="form['upload_oss'][item]" 
                                    :placeholder="item == 'domain' ? '自定义图片域名, 需解析至oss (http://img.baidu.com)' : item" 
                                    :key="index"
                                    style="margin-top:10px;"
                                ></el-input>
                            </template>
                        </template>
                    </template>

                    <template v-else-if="item.key == 'email_message'">
                        <el-switch @change="emailChange" v-model="form.email_message"></el-switch>
                    </template>

                    <template v-else>
                        <el-input v-model="form[item.key]" :placeholder="item.placeholder"></el-input>
                    </template>
                </el-form-item> 

            </template>

            <h2 class="tit hint-tit">首屏效果 
                  <el-popover
                    placement="top-start"
                    width="300"
                    trigger="hover"
                >
                    <p>背景颜色请选择类似图片颜色的透明度</p>
                    <img src="../assets/cover.png">
                    <span slot="reference">(view)</span>
                </el-popover>
            </h2>

            <template v-for="(v, k, i) in formList[1]">

                <el-form-item :label="v" :key="i+22">

                    <template v-if="k == 'image'">
                        <el-upload
                            action=""
                            class="cover"
                            :auto-upload="false"
                            :show-file-list="false"
                            :on-change="coverUpload"
                        >
                            <img v-if="cover['url'] || form.cover['image']" :src="cover['url'] || form.cover['image']">
                            <i v-else class="el-icon-plus"></i>
                        </el-upload>
                        <span class="upload-image-size">(1920*1080)</span>
                    </template>
                    
                    <template v-else-if="k == 'link'">
                        <el-input v-model="form['cover'][k]" placeholder="填写文章ID"></el-input>
                    </template>

                    <template v-else-if="k == 'color'">
                        <el-color-picker v-model="form['cover']['color']" show-alpha></el-color-picker>
                    </template>

                    <template v-else-if="k == 'icp_txt' || k == 'icp_link'">
                        <el-input v-model="form['cover'][k]" placeholder="网站的备案信息, 有则填写"></el-input>
                    </template>

                    <template v-else>
                        <el-input v-model="form['cover'][k]"></el-input>
                    </template>
                </el-form-item>

            </template>

            <h2 class="tit">页面音乐</h2>

            <template v-for="(v, k, i) in formList[3]">
                <el-form-item :label="v" :key="i+222">
                    <el-input v-model="form['bg'][k]" placeholder="填写音乐链接地址"></el-input>
                </el-form-item>
            </template>

            <h2 class="tit">修改密码</h2>

            <el-form-item label="原密码">
                <el-input v-model="password.one"></el-input>
            </el-form-item>

            <el-form-item label="新密码">
                <el-input v-model="password.two"></el-input>
            </el-form-item>

            <el-form-item>
                <el-button class="submit" type="primary" @click="onSubmit">立即保存</el-button>
                <span class="hint"><i class="el-icon-warning"></i>信息需填写完整才能正常使用</span>
            </el-form-item>
        </el-form>
    </div>
</template>

<script>
import { mapState } from 'vuex'
export default {
    data() {
        return {
            avatar: {},
            cover: {},
            formList: [
                [
                    {
                        key: 'avatar',
                        value: '管理员头像'
                    },
                    {
                        key: 'name',
                        value: '管理员昵称'
                    },
                    {
                        key: 'comment_mark',
                        value: '管理员标识'
                    },
                    {
                        key: 'web_name',
                        value: '网站昵称'
                    },
                    {
                        key: 'address',
                        value: '网站地址'
                    },
                    {
                        key: 'web_describe',
                        value: '网站描述'
                    },
                    {
                        key: 'web_seo',
                        value: 'SEO关键词'
                    },
                    {
                        key: 'email',
                        value: '评论邮箱'
                    },
                    {
                        key: 'email_name',
                        value: '评论昵称'
                    },
                    {
                        key: 'upload_type',
                        value: '文件上传'
                    },
                    {
                        key: 'email_message',
                        value: '评论通知'
                    },
                    {
                        key: 'email_pass',
                        value: '邮箱PASS',
                        placeholder: '与评论邮箱一致的PASS, 只支持QQ邮箱',
                        show: true
                    },
                ],
                {
                    image: '封面图',
                    title: '标题',
                    describe: '描述',
                    date: '时间',
                    link: '链接',
                    color: '色调',
                    icp_txt: '备案号',
                    icp_link: '备案链接',
                },
                ['bucket', 'region', 'endPoint', 'accessKeySecret', 'accessKeyId', 'domain'],
                {
                    bg_mood: '文章列表',
                    bg_letter: '短语列表',
                    bg_about: '个人介绍',
                }
            ],
            form: {
                cover: {},
                upload_oss: {},
                upload_type: '服务器',
                bg: {},
            },
            fullscreenLoading: false,

            password: {}
        }
    },
    mounted(){
        if(Object.keys(this.$info).length > 0){
            this.update()
        }
    },
    watch: {
        $info(){
            this.update()
        }
    },
    computed: {
        ...mapState(['$data']),
        $info(){
            return Object.keys(this.$data).length > 0 ? this.$data.info : {}
        }
    },
    methods: {
        update(){
            for(let i in this.$info){
                this.$set(this.form, i, this.$info[i])
            }
            this.$set(this.formList[0][11], 'show', !this.$info['email_message'])
        },
        async onSubmit(){
            /**
             * 修改密码
             */
            if(Object.keys(this.password).length > 0){
                if(!this.password['one']){
                    this.$message.error('请输入原密码!')
                    return;
                }else if(!this.password['two']){
                    this.$message.error('请填写新密码!')
                    return
                }

                this.fullscreenLoading = true;

                const result = await this.$http.post('/password', {password: this.password})
                if(result.data.status === 2){
                    this.fullscreenLoading = false;
                    this.$message.error('原密码输入有误!')
                    return;
                }
            }

            this.fullscreenLoading = true;

            /**
             * 图片上传
             */
            const img = ['avatar', 'cover']
            const upload = img.reduce((total, item) => {
                if(this[item].url){
                    total.push(new Promise((resolve, reject) => {
                        
                        this[item]['formData'].append('type', this.form.upload_type)

                        // 默认添加页面填写的oss信息, 其他地方上传以获取数据库为主
                        this[item]['formData'].append('upload_oss', JSON.stringify(this.form.upload_oss))

                        this.$http.post('/upload', this[item].formData).then(res => {
                            if(res.data.status == 100){
                                this[item] = {};
                                resolve({
                                    type: item, 
                                    url: res.data.url
                                })
                            }else{
                                reject()
                            }
                        }).catch(err => {
                            reject(err)
                        })
                    }))
                }
                return total
            }, [])

            Promise.all(upload).then(res => {
                if(res.length > 0){
                    res.map(item => {
                        if(item.type === 'avatar'){
                            this.form['avatar'] = item.url
                        }else{
                            this.form.cover['image'] = item.url
                        }
                    })
                }

                // 提交信息
                this.$http.post('/info', this.form).then(res => {
                    if(res.data.status === 1){
                        setTimeout(() => {
                            this.$message({
                                message: `更新成功`,
                                type: 'success'
                            });
                            this.$store.commit('updataInfo', res.data.body)
                            this.fullscreenLoading = false
                            this.$router.push('/')
                        }, 500)
                    }
                })
            }).catch(err => {
                this.fullscreenLoading = false
                this.$message.error('图片上传失败，请检查网络是否正常 or OSS信息是否填写正确!')
            })
        },
        emailChange(e){
            this.formList[0][11].show = !e
        },
        // 保存临时图片
        upload(type, file){
            if (!file.raw.type.includes('image')) {
                this.$message.error('请选择图片格式的文件!')
                return
            }
            const formData = new FormData();
            formData.append('file', file.raw);            
            this[type] = {
                url: URL.createObjectURL(file.raw),
                formData
            }

            if(type === 'avatar'){
                this.form['avatar'] = URL.createObjectURL(file.raw)
            }else{
                this.form['cover']['image'] = URL.createObjectURL(file.raw)
            }
        },
        avatarUpload(file){
            this.upload('avatar', file)
        },
        coverUpload(file){
            this.upload('cover', file)
        },
    }
}
</script>

<style lang="scss" scoped>
.avatar-uploader{
    height: 100px;
    /deep/ .el-upload{
        height: 100px;
        width: 100px;
        overflow: hidden;
        border: 4px solid #dce7ff;
        border-radius: 50%;
        display: flex;
        justify-content: center;
        align-items: center;
        i{
            font-size: 30px;
            color: #dcdfe6;
        }
        img{
            width: 100%;
        }
    }
}
.cover{
    overflow: hidden;
    /deep/ .el-upload{
        width: 300px;
        height: 170px;
        border-radius: 4px;
        overflow: hidden;
        border: 6px solid #dce7ff;
        display: flex;
        justify-content: center;
        align-items: center;
        img{
            width: 100%;
            height: 100%;
            border-radius: 2px;
        }
        i{
            font-size: 30px;
            color: #dcdfe6;
            vertical-align: middle;
        }
    }
}
.upload-image-size{
    position: absolute;
    top: 20px;
    font-size: 12px;
    right: calc(100% + 14px);
    color: #b3b3b3;
}
h2.tit{
    margin: 50px 0 30px;
    span{
        font-size: 12px;
        color: #b3b3b3;
    }
}

.el-popover {
    p{
        margin-bottom: 5px;
    }
    img{
        width: 100%;
        border-radius: 4px;
    }
}
.submit:hover + .hint{
    opacity: 1;
}
.hint{
    font-size: 12px;
    color: #ff4444;
    margin-left: 10px;
    letter-spacing: 2px;
    opacity: 0;
    transition: all .3s;
}
@media screen and (max-width: 600px) {
    .cover{
        /deep/ .el-upload{
            width: 100%;
        }
    }
    .hint{
        opacity: 1;
        display: block;
    }
    /deep/ .el-form-item{
        label{
            width: 90px !important;
        }
        .el-form-item__content{
            margin-left: 90px !important;
        }
    }
}
</style>