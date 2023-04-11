<template>
    <div class="categoryGoodsDiv">
        <!--      首页条-->
        <index-header></index-header>
        <div class="categoryGoodsDiv-1">
            <!--    网页主体-->

            <div class="parent">
  <div>
    <el-row style="position: relative;top:73px;">
      <p class="index-body-p-1">当前分类</p>
      <img :src="categoryInfo.image" :alt="categoryInfo.name">
      <br>
      <h2>
        {{ categoryInfo.name }}
      </h2>
    </el-row>
  </div>
</div>

    <el-carousel height="300px">
      <el-carousel-item v-for="item in items" :key="item">
        <img :src="item" alt="请检查网络连接" class="carousel-image" style="width: 50%; height: 100%;">
      </el-carousel-item>
      <el-carousel-item v-for="item in items" :key="item">
        <img :src="item" alt="请检查网络连接" class="carousel-image" style="width: 50%; height: 100%;">
      </el-carousel-item>
    </el-carousel>
    

    <el-carousel height="300px">
  <el-carousel-item v-for="(item, index) in items" :key="index">
    <div class="carousel-image-container">
        <img :src="item" alt="请检查网络连接" class="carousel-image" style="width: 50%; height: 100%;">
        <img :src="item" alt="请检查网络连接" class="carousel-image" style="width: 50%; height: 100%;">
    </div>
  </el-carousel-item>
</el-carousel>





            <el-row style="position: relative;top:73px;">
                <el-col :span="5" :offset="16">
                    <el-input placeholder="输入商品名、商品ID" v-model="search" clearable>
                    </el-input>
                </el-col>
                <el-col :span="2">
                    <el-button type="primary" icon="el-icon-search" @click="clickSearch"
                        v-loading.fullscreen.lock="fullscreenLoading" style="background-color: #232f3e;">搜索</el-button>
                </el-col>
            </el-row>
            <p class="index-body-p-1">最新发布</p>
            <hr />
            <el-row style="margin-top: 50px;" v-for="(obj1, index1) in getRowNums(data1)" :key="getRandomString(20)">
                <el-col :span="4" v-for="(obj2, index2) in mySlice(data1, index1 * 4, (index1 + 1) * 4)"
                    :key="getRandomString(21)" :offset="index2 > 0 ? 2 : 1">
                    <div @click="clickGoodsInfoButton(obj2.goodsId)">
                        <el-card :body-style="{ padding: '0px', textAlign: 'center' }">
                            <img :src="obj2.picture" class="image" width="100%" height="300" alt="请检查网络连接">
                            <div style="padding: 10px;">
                                <h4>{{ obj2.name }}</h4>
                                <div class="bottom clearfix">
                                    <el-button type="text" class="button">查看详情</el-button>
                                </div>
                            </div>
                        </el-card>
                    </div>
                </el-col>
            </el-row>

            <!--        分页组件-->
            <el-row style="text-align: center;margin-top: 80px;" v-show="paginationShow">
                <el-col :span="24">
                    <!--            限定每页 12 条数据-->
                    <el-pagination @current-change="handleCurrentChange" :current-page="currentPage" :page-sizes="[12]"
                        :page-size="pageSize" layout="prev, pager, next" :total="dataSearch.length">
                    </el-pagination>
                </el-col>
            </el-row>

            <p class="index-body-p-1" style="margin-top: 60px;">9 新以上</p>
            <hr />
            <el-row style="margin-top: 50px;" v-for="(obj1, index1) in getRowNums(data3)" :key="getRandomString(20)">
                <el-col :span="4" v-for="(obj2, index2) in mySlice(data3, index1 * 4, (index1 + 1) * 4)"
                    :key="getRandomString(21)" :offset="index2 > 0 ? 2 : 1">
                    <div @click="clickGoodsInfoButton(obj2.goodsId)">
                        <el-card :body-style="{ padding: '0px', textAlign: 'center' }">
                            <img :src="obj2.picture" class="image" width="100%" height="300" alt="请检查网络连接">
                            <div style="padding: 10px;">
                                <h4>{{ obj2.name }}</h4>
                                <div class="bottom clearfix">
                                    <el-button type="text" class="button">查看详情</el-button>
                                </div>
                            </div>
                        </el-card>
                    </div>
                </el-col>
            </el-row>




            <!--      商品详情弹出框-->
            <el-dialog :title="goodsInfoName" :visible.sync="centerDialogVisible" width="500px" height="1000px" center>
                <div style="text-align: center">
                    <el-carousel direction="horizontal" :autoplay="true">
                        <el-carousel-item v-for="(v, k) in goodsInfoImg" :key="k" style="width: 100%;height: 100%;">
                            <img :src="v" alt="请检查网络连接" width="200" height="400">
                        </el-carousel-item>
                    </el-carousel>
                    <h2><img src="https://i.loli.net/2019/12/17/NhTJjayS6ZCsHWP.png" alt="￥" width="30px">：{{ goodsInfoPrice
                    }}</h2>
                    <p>{{ goodsInfoDscrip }}</p>
                    <span slot="footer" class="dialog-footer">
                        <el-button type="primary" @click="addToShopCar" style="margin-top: 30px;">加入购物车</el-button>
                        <el-button type="primary" @click="talkToSeller" style="margin-top: 30px;">留言</el-button>
                    </span>
                </div>
            </el-dialog>

            <!--      加入购物车结果提示框-->
            <el-dialog title="提示" :visible.sync="centerDialogVisible2" width="30%" center>
                <div style="text-align: center">
                    <h4>{{ dialogValue }}</h4>
                    <span slot="footer" class="dialog-footer">
                        <el-row style="margin-top: 30px;">
                            <el-col :span="6" :offset="5">
                                <el-button type="primary" @click="clickButton" style="width: 100%">确 定</el-button>
                            </el-col>
                            <el-col :span="6" :offset="2">
                                <el-button type="primary" @click="clickGoToShopCar"
                                    :disabled="goToShopCar">前往购物车</el-button>
                            </el-col>
                        </el-row>
                    </span>
                </div>
            </el-dialog>

            <el-dialog title="给卖家留言" :visible.sync="messageDialogVisible" width="30%" center>
                <div style="text-align: center">
                    <el-form ref="messageForm" label-width="80px">
                        <el-form-item label="留言内容">
                            <el-input type="textarea" v-model="messageForm.message" rows="3"
                                placeholder="请输入留言内容"></el-input>
                        </el-form-item>
                    </el-form>
                    <span slot="footer" class="dialog-footer">
                        <el-button type="primary" @click="submitMessage">提交</el-button>
                        <el-button @click="messageDialogVisible = false">取消</el-button>
                    </span>
                </div>
            </el-dialog>

        </div>
        <!--      首页尾-->
        <index-footer></index-footer>
    </div>
</template>
  
<script>
import IndexHeader from './pages/index-header'
import IndexFooter from './pages/index-footer'
export default {
    name: "categoryGoods",
    data() {
        return {
                  items: {
        // img1: require("../../assets/school1.jpg"),
        img2: require("../assets/school2.jpg"),
        img3: require("../assets/school3.jpg"),
        img4: require("../assets/school4.jpg"),
        img5: require("../assets/school5.jpg")
      },
            categories: [
                {
                    id: 1,
                    name: '数码产品',
                    image: require('../assets/phone.png'),
                    route: 'categoryGoods'
                },
                {
                    id: 2,
                    name: '二手图书',
                    image: require('../assets/books.png'),
                    route: 'categoryGoods'
                },
                {
                    id: 3,
                    name: '生活百货',
                    image: require('../assets/products.png'),
                    route: 'categoryGoods'
                },
                {
                    id: 4,
                    name: '运动和户外',
                    image: require('../assets/sports.png'),
                    route: 'categoryGoods'
                },
                {
                    id: 5,
                    name: '美妆和护肤品',
                    image: require('../assets/cosmetic.png'),
                    route: 'categoryGoods'
                },
                {
                    id: 6,
                    name: '交通工具',
                    image: require('../assets/car.png'),
                    route: 'categoryGoods'
                },
                {
                    id: 7,
                    name: '更多',
                    image: require('../assets/more.png'),
                    route: 'categoryGoods'
                }
            ]
            ,
            categoryInfo: {
                categoryId: '',
                name: '',
                image: ''
            },
            categoryId: '',
            messageForm: {
                userId: '',
                goodsId: '',
                message: ''
            },
            rules: {
                search: [
                    { validator: checkString, trigger: 'blur' }
                ]
            },
            search: "",
            data1: [],//热门
            data2: [],//低价好物
            data3: [],//九新以上
            dataSearch: [],//用来保存搜索的全部结果
            goodsInfoId: "",
            goodsInfoImg: {},
            goodsInfoName: "",
            goodsInfoDscrip: "",
            goodsInfoPrice: 0,
            messageDialogVisible: false,
            centerDialogVisible: false,
            dialogValue: "",
            centerDialogVisible2: false,
            goToShopCar: true,
            currentPage: 1, //初始页
            pageSize: 12, //每页的数据
            paginationShow: false,//默认不显示分页
            searchFlag: false,//用来避免频繁向服务器发送数据
            fullscreenLoading: false,//模拟加载
        };
        let checkString = (rule, value, callback) => {
            if (!value) {
                return callback(new Error('请填写内容'));
            } else {
                callback();
            }
        };
    },
    mounted() {
        //加载数据
        this.categoryId = this.$route.query.id;
        const category = this.categories.find(item => item.id === this.categoryId);

        // 访问 `name` 和 `image` 属性
        this.categoryInfo.name = category.name;
        this.categoryInfo.image = category.image;
        this.categoryInfo.categoryId = this.categoryId;
        console.log("categoryInfo", this.categoryInfo);
        //加载热门精品数据，服务端返回最多 12条 数据
        let self = this;

        //延迟 0.3 S 绑定数据
        self.fullscreenLoading = true;
        setTimeout(() => {
            self.fullscreenLoading = false;
        }, 300);

        $.get("http://localhost:8083/category/newCategoryGoods/" + self.categoryId, function (data) {
            self.data1 = data;
            $(self.data1).each(function (index, element) {
                let jsonObj = {};
                jsonObj.goodsId = element.goodsId;
                //为每个表格元素加载图片数据，主图
                $.get("http://localhost:8083/goods/getGoodsMainImg.do", jsonObj, function (data) {
                    element.picture = "https://finaldesign-xcx.oss-cn-hangzhou.aliyuncs.com/" + data.imgUrl;
                    //因为数组单值更新不会引起 Vue 重新渲染，手动通知 Vue 渲染
                    self.$set(self.data1, index, element);
                }, "json");
            });
        }, "json");

        //加载 9 新以上数据，服务端返回最多 12条 数据
        $.get("http://localhost:8083/category/bestCategoryGoods/" + self.categoryId, function (data) {
            self.data3 = data;
            $(self.data3).each(function (index, element) {
                let jsonObj = {};
                jsonObj.goodsId = element.goodsId;
                //为每个表格元素加载图片数据，主图
                element.picture = "";
                $.get("http://localhost:8083/goods/getGoodsMainImg.do", jsonObj, function (data) {
                    element.picture = "https://finaldesign-xcx.oss-cn-hangzhou.aliyuncs.com/" + data.imgUrl;
                    //因为数组单值更新不会引起 Vue 重新渲染，手动通知 Vue 渲染
                    self.$set(self.data3, index, element);
                }, "json");
            });
        }, "json");
    },
    updated() {
        //当搜索框的值被清空
        //加载热门精品数据，服务端返回 12条 数据
        let self = this;
        //当搜索框无内容且已经搜索过，那么就是清空了搜索框，这时重新加载初始数据
        if (this.search === "" && this.searchFlag) {
            //加载热门精品数据，服务端返回最多 12条 数据
            $.get("http://localhost:8083/category/bestCategoryGoods/" + self.categoryId, function (data) {
                //延迟 0.3 S 绑定数据
                self.fullscreenLoading = true;
                setTimeout(() => {
                    self.fullscreenLoading = false;
                }, 300);
                self.data1 = data;
                self.dataSearch = [];//重置搜索结果内容为空数组
                self.searchFlag = false;
                $(self.data1).each(function (index, element) {
                    let jsonObj = {};
                    jsonObj.goodsId = element.goodsId;
                    //为每个表格元素加载图片数据，主图
                    $.get("http://localhost:8083/goods/getGoodsMainImg.do", jsonObj, function (data) {
                        //本地映射到9090端口，部署到远程服务器需要修改这里，服务端返回的imgUrl应该为相对路径，这里图片名字就行
                        element.picture = "https://finaldesign-xcx.oss-cn-hangzhou.aliyuncs.com/" + data.imgUrl;
                        //因为数组单值更新不会引起 Vue 重新渲染，手动通知 Vue 渲染
                        self.$set(self.data1, index, element);
                    }, "json");
                });
            }, "json");
            //隐藏分页组件
            this.paginationShow = false;
        }
    },
    components: {
        IndexHeader, IndexFooter
    },
    methods: {
       

        setCategory(id) {
            window.sessionStorage.setCategory('category', id);
            let session = window.sessionStorage.getItem("category");
            console.log("session",);
            this.$router.push(category.route);
        },
        //点击查看详情
        clickGoodsInfoButton(goodsId) {
            this.centerDialogVisible = true;
            this.goodsInfoId = goodsId;
            let self = this;
            let jsonObj = {};
            jsonObj.goodsId = goodsId;
            let jsonMsg = JSON.stringify(jsonObj);
            $.get("http://localhost:8083/goods/getGoodsById.do", jsonObj, function (data) {
                self.goodsInfoName = data.name;
                self.goodsInfoPrice = data.price;
                self.goodsInfoDscrip = data.dscrip;
                $.get("http://localhost:8083/goods/getGoodsImgMap.do", jsonObj, function (data) {
                    self.goodsInfoImg = data;
                }, "json");
            }, "json");
        },

        talkToSeller() {
            // 打开留言弹出框
            this.messageDialogVisible = true;
            // 设置商品id和用户id
            this.messageForm.goodsId = this.goodsInfoId;
            this.messageForm.userId = window.sessionStorage.getItem("userId");
            console.log('messageForm:', this.messageForm);
        },


        //点击加入购物车按钮
        addToShopCar() {
            let jsonObj = {};
            jsonObj.userId = window.sessionStorage.getItem("userId");
            jsonObj.goodsId = this.goodsInfoId;
            let jsonMsg = JSON.stringify(jsonObj);
            let self = this;
            $.post("http://localhost:8083/shopCar/addOneToShopCar.do", jsonMsg, function (data) {
                if (data.code === 1) {
                    self.dialogValue = "加入购物车成功";
                    self.goToShopCar = false;
                } else {
                    self.dialogValue = "加入购物车失败，错误码：" + data.code;
                    self.goToShopCar = true;
                }
            }, "json");
            this.centerDialogVisible2 = true;
        },
        // 获取长度为len的随机字符串
        getRandomString(len) {
            len = len || 32;
            let $chars = 'ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678';
            let maxPos = $chars.length;
            let pwd = '';
            for (let i = 0; i < len; i++) {
                pwd += $chars.charAt(Math.floor(Math.random() * maxPos));
            }
            return pwd;
        },
        clickButton() {
            this.centerDialogVisible = false;
            this.centerDialogVisible2 = false;
        },
        submitMessage() {
            console.log('messageForm:', this.messageForm);
            let json = this.messageForm;
            // 将数据转换成JSON字符串
            const jsonData = JSON.stringify(json);
            console.log('jsonData:', jsonData); // 打印jsonData
            // 调用接口提交数据
            $.post('http://localhost:8083/message/send', jsonData)
                .then(res => {
                    // 提交成功后的操作
                    this.$message.success('留言成功');

                    this.messageDialogVisible = false;
                })
                .catch(err => {
                    // 提交失败的操作
                    this.$message.error('留言失败，请稍后再试');
                });
        },



        //点击跳转购物车按钮
        clickGoToShopCar() {
            this.clickButton();
            this.$router.push('shopCar');
        },
        // 初始页currentPage、初始每页数据数pageSize和数据data
        // 点击了分页组件换页按钮
        handleCurrentChange(currentPage) {
            this.currentPage = currentPage;
            // console.log(this.currentPage)  //点击第几页
            //切割 dataSearch 赋值给 data1
            // this.data1 = this.dataSearch.slice((this.currentPage-1)*12,this.currentPage*12);
            this.data1 = this.mySlice(this.dataSearch, (this.currentPage - 1) * 12, this.currentPage * 12);
        },
        //点击搜索按钮
        clickSearch() {
            if (this.search !== "") {
                let self = this;

                //延迟 0.3 S 绑定数据
                self.fullscreenLoading = true;
                setTimeout(() => {
                    self.fullscreenLoading = false;
                }, 300);

                let jsonObj = {};
                jsonObj.text = this.search;
                $.get("http://localhost:8083/goods/getSearchGoods.do", jsonObj, function (data) {
                    //保存搜索内容
                    self.dataSearch = data;
                    $(self.dataSearch).each(function (index, element) {
                        let jsonObj = {};
                        jsonObj.goodsId = element.goodsId;
                        //为每个表格元素加载图片数据，主图
                        $.get("http://localhost:8083/goods/getGoodsMainImg.do", jsonObj, function (data) {
                            //本地映射到9090端口，部署到远程服务器需要修改这里，服务端返回的imgUrl应该为相对路径，这里图片名字就行
                            element.picture = "https://finaldesign-xcx.oss-cn-hangzhou.aliyuncs.com/" + data.imgUrl;
                            //因为数组单值更新不会引起 Vue 重新渲染，手动通知 Vue 渲染
                            self.$set(self.dataSearch, index, element);
                        }, "json");
                    });
                    //设置搜索过，用于清空搜索框时还原初始数据
                    self.searchFlag = true;

                    //延迟 0.8 S 绑定数据
                    self.fullscreenLoading = true;
                    setTimeout(() => {
                        self.fullscreenLoading = false;
                    }, 800);

                    //当 dataSearch 的长度大于 12，显示分页组件，否则隐藏
                    if (self.dataSearch.length > 12) {
                        self.paginationShow = true;
                    } else {
                        self.paginationShow = false;
                    }

                    //如果分页组件被展示，切割 data1
                    if (self.paginationShow === true) {
                        // this.data1 = this.dataSearch.slice((this.currentPage-1)*12,this.currentPage*12);
                        self.data1 = self.mySlice(self.dataSearch, (self.currentPage - 1) * 12, self.currentPage * 12);
                        // console.log(123);
                    } else {
                        //如果没有展示分页组件，并且搜索了数据
                        self.data1 = self.dataSearch;
                        // console.log(this.dataSearch);
                    }
                }, "json");
            }
        },
        //计算打印的行数
        getRowNums(data) {
            let len = parseInt(data.length / 4);
            if (len === 0) {
                return 1;
            } else {
                if (data.length - len * 4 > 0) {
                    return len + 1;
                } else {
                    return len;
                }
            }
        },
        //数组切割
        mySlice(data, indexStart, indexEnd) {
            if (indexStart < 0)
                indexStart = 0;
            if (data.length >= indexEnd) {
                return data.slice(indexStart, indexEnd);
            } else {
                if (indexStart < data.length) {
                    return data.slice(indexStart, data.length);
                } else {
                    return [];
                }
            }
        }
    }
}
</script>
  
<style scoped>
.categoryGoodsDiv-1 {
    width: 80%;
    margin: 0 auto;
}

.index-body-p-1 {
    font-weight: bold;
    font-size: 30px;
    color: black;
    margin-top: 30px;
}

.carousel-item img {
    object-fit: cover;
    height: 370px;
    width: 100%;

}

.carousel-image {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    object-fit: cover;
}

.el-card {
    width: 200px;
    /* 控制卡片宽度 */
    height: 400px;
    /* 控制卡片高度 */
}

.el-card .image {
    max-width: 100%;
    /* 图片自适应缩放 */
    max-height: 100%;
}


/*logo*/
.category-section {
    margin: 0 0;
}

.category-section h2 {
    font-size: 1.5rem;
    font-weight: 500;
    margin-bottom: 10px;
}

.category-list {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    padding: 0;
    margin: 0;
    list-style: none;
}

.category-list li {
    width: calc((100% - 140px) / 7);
    margin-right: 20px;
}

.category-list li a {
    display: block;
    border: 1px solid #ccc;
    border-radius: 5px;
    text-align: center;
    font-size: 1.2rem;
    color: #444;
    text-decoration: none;
    padding: 0.8rem;
    transition: background-color 0.2s ease;
}

.category-list li h2 {
    color: #202a59;
}

.category-list li a:hover {
    background-color: #f5f5f5;
    transform: translateY(-5px);
}

.parent {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100; /* 可以根据需要调整高度 */
}

/*  轮播图 */
.el-carousel__item h3 {
    color: #475669;
    font-size: 14px;
    opacity: 0.75;
    line-height: 150px;
    margin: 0;
  }

  .el-carousel__item:nth-child(2n) {
     background-color: #99a9bf;
  }
  
  .el-carousel__item:nth-child(2n+1) {
     background-color: #d3dce6;
  }
  .carousel-image-container {
  display: flex;
}
.carousel-image {
  width: 50%;
  height: 100%;
}
</style>
