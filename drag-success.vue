<!-- ============================================================== -->
<!-- f12902 页面铺码（答题区域）添加 -->
<!-- ============================================================== -->
<template>

    <!-- ============================================================== -->
    <!-- Start Page Content -->
    <!-- ============================================================== -->

    <div class="row">
        <!-- 加载进度条 -->
        <P_Load_Circular ref="P_Load_Circular"></P_Load_Circular>

        <div class="col-12">
            <div class="card">
                <div class="row" style="background-color: #c2dcf3; margin: 0;">
                    <div class="col-md-2 text-right pt-5">
                        <div>
                            <div class="text-left mt-3">操作：</div>
                            <div class="mt-3">
                                <button :disabled="!isBind" type="button" :class="isBind ? '' : 'cursor-not'" class="btn waves-effect waves-light btn-info" @click="addElement">添加</button>
                            </div>
                            <div class="mt-3">
                                <template v-if="isBind">
                                    <button disabled type="button" class="btn waves-effect waves-light btn-info cursor-not" @click="returnElement">返回</button>
                                </template>
                                <template v-else>
                                    <button :disabled="!isSubject" type="button" class="btn waves-effect waves-light btn-info" @click="returnElement">返回</button>
                                </template>
                            </div>
                            <div class="mt-3">
                                <button :disabled="!isBind" type="button" class="btn waves-effect waves-light btn-info" @click="bigFunc">放大</button>
                            </div>
                            <div class="mt-3">
                                <button :disabled="!isBind" type="button" class="btn waves-effect waves-light btn-info" @click="smallFunc">缩小</button>
                            </div>
                            <div class="switch mt-3">
                                <label @click="getChecked" :class="isBind ? '' : 'cursor-not'">
                                    <span>坐标：</span>
                                    <input id="isShow" :disabled="!isBind" type="checkbox" checked>
                                    <span :class="isBind ? '' : 'cursor-not'" class="lever switch-col-light-blue" style="margin-right: 7px;"></span>
                                </label>
                            </div>
                            <div class="mt-3">
                                <button type="button" :disabled="!isBind" :class="isBind ? '' : 'cursor-not'" class="btn waves-effect waves-light btn-info" @click="submitElement">保存</button>
                            </div>
                            <div class="mt-3" v-for="(item, index) in imgList">
                                <button v-if="imgOrder == index" type="button" :disabled="!isBind" :class="isBind ? '' : 'cursor-not'" class="btn waves-effect waves-light btn-outline-secondary active" @click="selectImg(index)">第{{index + 1}}张</button>
                                <button v-else type="button" :disabled="!isBind" :class="isBind ? '' : 'cursor-not'" class="btn waves-effect waves-light btn-outline-secondary" @click="selectImg(index)">第{{index + 1}}张</button>
                            </div>
                            <div class="mt-3 mb-3">
                                <router-link to="/F13901">
<!--                                <router-link to="/F12901">-->
                                    <button type="button" class="btn waves-effect waves-light btn-info">取消</button>
                                </router-link>
                            </div>
                        </div>
                    </div>
                    <div class="col-md-10" style="overflow: auto;">

                        <!-- 题目列表 -->
                        <div class="p-20" v-if="isSubject">
                            <h4 class="card-title">
                                <span>题目列表</span>

                                <button v-if="1 === currentTag" type="button" class="btn waves-effect waves-light btn-primary ml-5 mr-3" @click="changeTag(1)">切题</button>
                                <button v-else type="button" class="btn waves-effect waves-light ml-5 mr-3" @click="changeTag(1)">切题</button>
                                <button v-if="2 === currentTag" type="button" class="btn waves-effect waves-light btn-primary" @click="changeTag(2)">答题</button>
                                <button v-else type="button" class="btn waves-effect waves-light" @click="changeTag(2)">答题</button>
                            </h4>
                            <div class="list-group">
                                <div class="list-group-item" v-for="(item, index) in subjectList" :key="index">
                                    <span>{{item.subjectTitle}}</span>
                                    <template v-for="(answerItem, idx) in item.subjectAnswerFormList">
                                        <button v-if="1 === currentTag" type="button" class="btn waves-effect btn-outline-secondary ml-2" @click="selectSubject(item, idx, 1)">切题</button>
                                        <button v-if="2 === currentTag" type="button" class="btn waves-effect btn-rounded btn-outline-secondary ml-2" @click="selectSubject(item, idx, 2)" :key="idx">答题{{idx + 1}}</button>
                                    </template>
                                </div>
                            </div>
                        </div>

                        <div class="card-main" :style="'width:'+ (baseWidth + 17) + 'px;height:' + (baseHeight + 17) + 'px'" v-if="isBind">
                            <!-- 添加遮罩 -->
                            <div class="card-main-content" :style="'width:'+ baseWidth + 'px;height:' + baseHeight + 'px'">
                                <template v-for="(item, index) in imgList">
                                    <img v-if="index === imgOrder" :key="index" class="card-main-img" :class="isSize ? 'img-size' : ''" @load="getImgAttributes" :src="item" alt="">
                                </template>
                            </div>

                            <div id="mainContainer" :style="'width:'+ baseWidth + 'px;height:' + maskHeight + 'px'">
                                <div id="innerContainer">
                                    <template v-for="(item, index) in domCount">
                                        <template v-if="currentTag === item.tagType">
                                            <div v-if="item.pageNum == imgOrder" class="element-item" :key="index" @mousedown="moveBind($event, item)" @mousemove="moveStart($event)" :style="'width:'+item.omitWpx+'px;height:'+item.omitHpx+'px; left:'+item.omitXpx+'px;top:'+item.omitYpx+'px; position:absolute;'">
                                                <div class="" v-if="1 === item.tagType" :style="'width:100%;height:100%;background: #1e88e5; opacity: 0.5;'"></div>
                                                <div class="" v-if="2 === item.tagType" :style="'width:100%;height:100%;background: #1304f7; opacity: 0.5;'"></div>
                                                <div v-if="isShow" class="" :omitName="item.subjectTitle" :answerOrder="item.answerOrder" style="position: absolute;top: -72px;font-size: 14px;left: 0;background-color: #ffffff;" @mousedown.stop="">
                                                    <span>{{item.xaxis}},{{item.yaxis}}<br/>{{item.width}},{{item.height}}</span>
                                                    <div class="omit-name" v-if="2 === item.tagType">{{item.subjectTitle}}（答题{{item.answerOrder}}）</div>
                                                    <div class="omit-name" v-else-if="1 === item.tagType">{{item.subjectTitle}}（切题）</div>
                                                </div>
                                                <div class='' style='width: 18px;height:18px;line-height: 1px;text-align: center;background-color: #ddd;border: 1px solid #000;border-radius: 50%;top: -9px;right: -9px;position:absolute;cursor: pointer;' @mousedown.stop="zoomDelete($event, item)"><i class="fa fa-times"></i></div>
                                                <div class='resize' style='width: 7px;height:7px;background-color: #ddd;border: 1px solid #000;bottom: -5px;right: -5px;position:absolute;cursor: nw-resize !important;' @mousedown.stop="zoomBind($event, item)"></div>
                                            </div>
                                        </template>
                                    </template>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- 弹出消息框:错误警告类型 -->
        <M_Alert ref="M_Alert" :callBack="callBackAlert"></M_Alert>
    </div>

    <!-- ============================================================== -->
    <!-- End Page Content -->
    <!-- ============================================================== -->

</template>
<script>
    import B00008 from 'SrcRootPath/pages/b0/b00008/b00008.vue';
    import {teapotAjax2} from 'teapot-ajax';
    import P_Load_Circular from "SrcRootPath/../p00/components/b00002/p_load_circular.vue";
    import M_Alert from "SrcRootPath/../p00/components/b00001/m_alert.vue";
    import ApiUrlConstants from '../../../constants/ApiUrlConstants'
    export default {
        components: {
            B00008,
            P_Load_Circular,
            M_Alert,
        },
        data() {
            return {
                callBackAlert: null,
                subjectList: [],
                // 题目列表开关
                isSubject: false,
                // 添加遮罩开关
                isBind: true,
                // 点码编号
                pointCodeItem: {},
                baseCount: 5,
				baseTimes: 0.8,
                baseWidth: 0,
                baseHeight: 0,
                maskHeight: 0,
				isSize: false,
                containerId: "innerContainer",
                moveElement: "div",
                //是否元素移动。
                isMouseMove: false,
                //是否元素缩放。
                isMouseZoom: true,
                domCount: [],
                isShow: true,
                commData: {
                    apiAddrss: "", // api服务器地址
                    token: "", // token
                },
                imgList: [],
                // 第几张
                imgOrder: 0,
                currentTag: 1
            }
        },
        created() {
            // 恢复数据
            this.commData = this.$store.getters.getCommData;

            // 1.记录当前的路由与页面显示参数，以便恢复页面用
            this.$saveLastOpt(this,null);
        },
        mounted() {
            let _this = this;
            _this.saveOrResumeState();

            _this.pointCodeItem = JSON.parse(sessionStorage.getItem('pointCodeItem'));
            _this.selectList();

            window.onload = function(){
                //禁止鼠标选中。
                document.onselectstart = new Function("event.returnValue=false;");
                //获得容器坐标。
                let setContainer = _this.findPosition(document.getElementById(_this.containerId));

                $("#ContainerWidth").attr("value", setContainer[4]);
                $("#ContainerHeight").attr("value", setContainer[5]);

                _this.isSubject = false;
                _this.isBind = true;
            }
        },
        methods: {
            // 刷新页面后，恢复页面用
            saveOrResumeState() {
                // 1.记录当前的路由与页面显示参数，以便恢复页面用
                this.$saveLastOpt(this,null);
            },

            // 选择
            selectList () {
                // 根据题库id集合获得题库信息
                let _this = this;

                _this.isSubject = false;

                // 准备检索条件
                let searchParams = {};

                // 进度条开始加载
                _this.$refs.P_Load_Circular.show();

                let cacheArr = [];
                for (let idx = 0; idx < _this.pointCodeItem.subjectFormList.length; idx++) {
                    cacheArr.push(_this.pointCodeItem.subjectFormList[idx].subjectId);
                }

                let apiurl = _this.commData.apiAddrss + ApiUrlConstants.S1203161002 + '?subjectIds=' + cacheArr;
                teapotAjax2.get(apiurl, _this.commData.token, searchParams, _this, function (data) {
                    // 后台服务异常处理
                    if (200 !== data.statusCode) {
                        console.log("data.statusCode:"+ data.statusCode + " msg:"+ data.msg);
                        // 关闭进度条
                        _this.$refs.P_Load_Circular.close();
                        // 显示消息框
                        _this.$refs.M_Alert.show(data.msg);
                    } else {    // 200 一切正常
                        if ("200" === data.msgCode) { // 正常检索
                            if (data.dataList) {
                                _this.subjectList = JSON.parse(data.dataList);
                            }
                        }
                        // 关闭进度条
                        _this.$refs.P_Load_Circular.close();
                        _this.getSubjectImg();
                    }
                })
            },

            // 加载题目图片
            getSubjectImg () {
                let _this = this;
                let searchParams = {
                    resultId: _this.pointCodeItem.pointCodeResultForm.resultId,
                    resultType: 1
                };
                _this.imgList = [];
                let apiurl = _this.commData.apiAddrss + ApiUrlConstants.S120122102;
                teapotAjax2.get(apiurl, _this.commData.token, searchParams, _this, function (data) {
                    // 后台服务异常处理
                    if (200 !== data.statusCode) {
                        console.log("data.statusCode:" + data.statusCode + " msg:" + data.msg);
                        // 关闭进度条
                        _this.$refs.P_Load_Circular.close();
                        // 显示消息框
                        _this.$refs.M_Alert.show("服务器暂时不能使用，请稍后再试！！！");
                    } else {    // 200 一切正常
                        if ("200" === data.msgCode) { // 正常检索
                            if (data.dataList && undefined !== data.dataList && 0 < data.dataList.length) {
                                let tempDataList = JSON.parse(data.dataList);
                                for (let index = 0; index < tempDataList.length; index++) {
                                    _this.imgList.push(_this.commData.fileAddrss+ "/" + tempDataList[index].codeFile.replace("@", "/"));
                                }
                                // 关闭进度条
                                _this.$refs.P_Load_Circular.close();
                                _this.getPointCodeDetail();
                            }
                        }
                    }
                })
            },

            // 加载铺码数据
            getPointCodeDetail () {
                let _this = this;
                let searchParams = {
                    pointCodeId: _this.pointCodeItem.pointCodeResultForm.pointCodeId
                };
                let apiurl = _this.commData.apiAddrss + ApiUrlConstants.S120126101;
                teapotAjax2.get(apiurl, _this.commData.token, searchParams, _this, function (data) {
                    // 后台服务异常处理
                    if (200 !== data.statusCode) {
                        console.log("data.statusCode:" + data.statusCode + " msg:" + data.msg);
                        // 关闭进度条
                        _this.$refs.P_Load_Circular.close();
                        // 显示消息框
                        _this.$refs.M_Alert.show("服务器暂时不能使用，请稍后再试！！！");
                    } else {    // 200 一切正常
                        if ("200" === data.msgCode) { // 正常检索
                            if (data.dataList && undefined !== data.dataList && 0 < data.dataList.length) {
                                let tempDataList = JSON.parse(data.dataList);
                                // 关闭进度条
                                _this.$refs.P_Load_Circular.close();
                                _this.reductionPointCodeDom(tempDataList);
                            }
                        }
                    }
                })
            },

            // 还原已铺码的数据
            reductionPointCodeDom (tempDataList) {
                this.domCount = [];
                // this.imgList
                // this.subjectList

                // 获取屏幕DPI
                let dpi = this.conversion_getDPI()[0];
                // 根据dpi计算屏幕和A4的比例
                let smm1 = this.baseWidth / dpi * 25.4;
                let amm = 210;
                let proportion1 = smm1 / amm;
                if (0 === proportion1) {
                    // 第一次加载this.baseWidth为0就设置一个初始值
                    proportion1 = Number(3.125863095238095);
                }

                for (let index = 0; index < tempDataList.length; index++) {
                    for (let idx = 0; idx < this.subjectList.length; idx++) {
                        if (tempDataList[index].subjectId === this.subjectList[idx].subjectId) {
                            let domItem = {};
                            if (3 === this.subjectList[idx].subjectType || 7 === this.subjectList[idx].subjectType) {
                                for (let inx = 0; inx < this.subjectList[idx].subjectAnswerFormList.length; inx++) {
                                    let answerItem = this.subjectList[idx].subjectAnswerFormList[inx];
                                    if (tempDataList[index].propertyId === answerItem.propertyId) {
                                        domItem.subjectId = tempDataList[index].subjectId;
                                        domItem.pageNum = tempDataList[index].pageNum;
                                        domItem.propertyId =  answerItem.propertyId;
                                        domItem.answerOrder = answerItem.propertyId + 1;
                                        domItem.pointCodeId = tempDataList[index].pointCodeId;
                                        domItem.subjectTitle = this.subjectList[idx].subjectTitle;
                                        domItem.xaxis = tempDataList[index].xaxis;
                                        domItem.yaxis = tempDataList[index].yaxis;
                                        domItem.width = tempDataList[index].width;
                                        domItem.tagType = tempDataList[index].tagType;
                                        domItem.height = tempDataList[index].height;
                                        domItem.dpi = tempDataList[index].dpi;
                                        domItem.omitXpx = Number(tempDataList[index].xaxis) * proportion1 / 25.4 * dpi;
                                        domItem.omitYpx = Number(tempDataList[index].yaxis) * proportion1 / 25.4 * dpi;
                                        domItem.omitWpx = Number(tempDataList[index].width) * proportion1 / 25.4 * dpi;
                                        domItem.omitHpx = Number(tempDataList[index].height) * proportion1 / 25.4 * dpi;
                                    }
                                }
                            } else {
                                if (tempDataList[index].propertyId === this.subjectList[idx].subjectAnswerFormList[0].propertyId) {
                                    domItem.subjectId = tempDataList[index].subjectId;
                                    domItem.pageNum = tempDataList[index].pageNum;
                                    domItem.propertyId =  this.subjectList[idx].subjectAnswerFormList[0].propertyId;
                                    domItem.answerOrder = this.subjectList[idx].subjectAnswerFormList[0].propertyId;
                                    domItem.pointCodeId = tempDataList[index].pointCodeId;
                                    domItem.subjectTitle = this.subjectList[idx].subjectTitle;
                                    domItem.xaxis = tempDataList[index].xaxis;
                                    domItem.yaxis = tempDataList[index].yaxis;
                                    domItem.width = tempDataList[index].width;
                                    domItem.height = tempDataList[index].height;
                                    domItem.tagType = tempDataList[index].tagType;
                                    domItem.omitXpx = Number(tempDataList[index].xaxis) * proportion1 / 25.4 * dpi;
                                    domItem.omitYpx = Number(tempDataList[index].yaxis) * proportion1 / 25.4 * dpi;
                                    domItem.omitWpx = Number(tempDataList[index].width) * proportion1 / 25.4 * dpi;
                                    domItem.omitHpx = Number(tempDataList[index].height) * proportion1 / 25.4 * dpi;
                                }
                            }

                            this.domCount.push(domItem);
                        }
                    }
                }
            },

            // 选择显示第几张
            selectImg (index) {
                this.imgOrder = index;
                this.isBind = true;
                this.isSubject = false;
            },

            // 选择切题or答题
            changeTag (type) {
                this.currentTag = type;
            },

            // 选择题目
            selectSubject: function (item, idx, tagType) {
                let domIndex = -1;
                let imgOrder = -1;
                for (let index = 0; index < this.domCount.length; index++) {
                    if (item.subjectId === this.domCount[index].subjectId && this.domCount[index].answerOrder === (idx + 1)) {
                        domIndex = index;
                        imgOrder = this.domCount[index].pageNum;
                        // break;
                    }
                }

                if (-1 < domIndex && this.domCount[domIndex].answerOrder === (idx + 1) && this.imgOrder === this.domCount[domIndex].pageNum && tagType === this.domCount[domIndex].tagType) {
                    // this.$refs.M_Alert.show("选中项已存在于第"+(imgOrder+1)+"张");
                    this.$refs.M_Alert.show("当前页选中项已存在");
                } else {
                    // 获取屏幕DPI
                    let dpi = this.conversion_getDPI()[0];
                    let x1, y1, w1, h1;
                    if (5 === this.baseCount) {
						x1 = 180 / 2 / dpi * 25.4;
						y1 = 180 / 2 / dpi * 25.4;
						w1 = 50 / 2 / dpi * 25.4;
						h1 = 30 / 2 / dpi * 25.4;
                    } else if (5 > this.baseCount) {
						x1 = 180 * this.baseTimes / 2 / dpi * 25.4;
						y1 = 180 * this.baseTimes / 2 / dpi * 25.4;
						w1 = 50 * this.baseTimes / 2 / dpi * 25.4;
						h1 = 30 * this.baseTimes / 2 / dpi * 25.4;
                    } else {
						x1 = 180 / this.baseTimes / 2 / dpi * 25.4;
						y1 = 180 / this.baseTimes / 2 / dpi * 25.4;
						w1 = 50 / this.baseTimes / 2 / dpi * 25.4;
						h1 = 30 / this.baseTimes / 2 / dpi * 25.4;
                    }
                    // 根据dpi计算屏幕和A4的比例
                    let smm1 = this.baseWidth / dpi * 25.4;
                    let amm = 210;
                    let proportion1 = smm1 / amm;
                    if (0 === proportion1) {
                        // 第一次加载this.baseWidth为0就设置一个初始值
                        proportion1 = Number(3.125863095238095);
                    }

                    this.isSubject = false;
                    this.isBind = true;


                    // New Object的封装
                    let domItem = {};
                    domItem.dpi = dpi;
                    domItem.pageNum = this.imgOrder;
					if (5 === this.baseCount) {
						domItem.omitXpx = 180 / 2;
						domItem.omitYpx = 180 / 2;
						domItem.omitWpx = 50 / 2;
						domItem.omitHpx = 30 / 2;
					} else if (5 > this.baseCount) {
						domItem.omitXpx = 180 * this.baseTimes / 2;
						domItem.omitYpx = 180 * this.baseTimes / 2;
						domItem.omitWpx = 50 * this.baseTimes / 2;
						domItem.omitHpx = 30 * this.baseTimes / 2;
					} else {
						domItem.omitXpx = 180 / this.baseTimes / 2;
						domItem.omitYpx = 180 / this.baseTimes / 2;
						domItem.omitWpx = 50 / this.baseTimes / 2;
						domItem.omitHpx = 30 / this.baseTimes / 2;
					}
                    domItem.xaxis = x1 / proportion1;
                    domItem.yaxis = y1 / proportion1;
                    domItem.width = w1 / proportion1;
                    domItem.height = h1 / proportion1;
                    domItem.answerOrder = idx + 1;
                    domItem.subjectId = item.subjectId;
                    // domItem.propertyId =  item.propertyId;
                    domItem.propertyId =  item.subjectAnswerFormList[idx].propertyId;
                    domItem.pointCodeId = this.pointCodeItem.pointCodeResultForm.pointCodeId;
                    domItem.subjectTitle = item.subjectTitle;
                    domItem.tagType = tagType;
                    this.domCount.push(domItem);



                    // // 用item数据的封装
                    // let domItem = JSON.parse(JSON.stringify(item))
                    // domItem.imgOrder = this.imgOrder;
                    // domItem.omitXpx = 180;
                    // domItem.omitYpx = 90;
                    // domItem.omitWpx = 50;
                    // domItem.omitHpx = 30;
                    // domItem.xaxis = x1 / proportion1;
                    // domItem.yaxis = y1 / proportion1;
                    // domItem.width = w1 / proportion1;
                    // domItem.height = h1 / proportion1;
                    // domItem.answerOrder = idx + 1;
                    // this.domCount.push(domItem);
                }
            },

            // 保存
            submitElement: function () {
                console.log(this.domCount);

                let _this = this;
                // 进度条开始加载
                _this.$refs.P_Load_Circular.show();

                // 提交题目数据
                let apiurl = _this.commData.apiAddrss + ApiUrlConstants.S120126201;

                teapotAjax2.post(apiurl, _this.commData.token, this.domCount, _this, function (data) {
                    // 后台服务异常处理
                    if (201 !== data.statusCode) {
                        console.log("data.statusCode:"+ data.statusCode + " msg:"+ data.msg);
                        // 关闭进度条
                        _this.$refs.P_Load_Circular.close();
                        // 显示消息框
                        _this.$refs.M_Alert.show(data.msg);
                        // _this.$refs.M_Alert.show("服务器暂时不能使用，请稍后再试！！！");
                    } else {
                        // 200 一切正常
                        if ("201" === data.msgCode) { // 正常添加
                            // 显示消息框
                            _this.$refs.M_Alert.show("保存成功");
                            _this.callBackAlert = function () {
                                let _this = this;
                                // _this.$router.push({name: 'F12901'});
                                _this.$router.push({name: 'F13901'});
                            };
                            // 关闭进度条
                            _this.$refs.P_Load_Circular.close();
                        }
                    }
                })
            },

            bigFunc: function () {
				let _this = this;
				if (8 > _this.baseCount) {
					_this.baseWidth = _this.baseWidth / _this.baseTimes;
					_this.baseHeight = _this.baseHeight / _this.baseTimes;
					_this.maskHeight = _this.maskHeight / _this.baseTimes;
					for (let index = 0; index < _this.domCount.length; index++) {
						_this.domCount[index].omitHpx = _this.domCount[index].omitHpx / _this.baseTimes;
						_this.domCount[index].omitWpx = _this.domCount[index].omitWpx / _this.baseTimes;
						_this.domCount[index].omitXpx = _this.domCount[index].omitXpx / _this.baseTimes;
						_this.domCount[index].omitYpx = _this.domCount[index].omitYpx / _this.baseTimes;
						// _this.domCount[index].width = _this.domCount[index].width / _this.baseTimes;
						// _this.domCount[index].height = _this.domCount[index].height / _this.baseTimes;
						// _this.domCount[index].xaxis = _this.domCount[index].xaxis / _this.baseTimes;
						// _this.domCount[index].yaxis = _this.domCount[index].yaxis / _this.baseTimes;
						_this.$set(_this.domCount, index, _this.domCount[index]);
					}
					_this.baseCount++;
                }
            },

            smallFunc: function () {
            	let _this = this;
            	if (0 < _this.baseCount) {
					_this.baseWidth = _this.baseWidth * _this.baseTimes;
					_this.baseHeight = _this.baseHeight * _this.baseTimes;
					_this.maskHeight = _this.maskHeight * _this.baseTimes;
					for (let index = 0; index < _this.domCount.length; index++) {
						_this.domCount[index].omitHpx = _this.domCount[index].omitHpx * _this.baseTimes;
						_this.domCount[index].omitWpx = _this.domCount[index].omitWpx * _this.baseTimes;
						_this.domCount[index].omitXpx = _this.domCount[index].omitXpx * _this.baseTimes;
						_this.domCount[index].omitYpx = _this.domCount[index].omitYpx * _this.baseTimes;
						// _this.domCount[index].width = _this.domCount[index].width * _this.baseTimes;
						// _this.domCount[index].height = _this.domCount[index].height * _this.baseTimes;
						// _this.domCount[index].xaxis = _this.domCount[index].xaxis * _this.baseTimes;
						// _this.domCount[index].yaxis = _this.domCount[index].yaxis * _this.baseTimes;
						_this.$set(_this.domCount, index, _this.domCount[index]);
					}
					_this.baseCount--;
                }
            },

            // 绑定移动
            moveBind: function (evnt, omitItem) {
                let _this = this;
                _this.isMouseMove = true;

                //获得元素坐标。
                let left = evnt.currentTarget.offsetLeft;
                let top = evnt.currentTarget.offsetTop;
                let width = evnt.currentTarget.offsetWidth;
                let height = evnt.currentTarget.offsetHeight;

                //计算出鼠标的位置与元素位置的差值。
                let cleft = evnt.clientX - left;
                let ctop = evnt.clientY - top;

                //获得容器坐标。
                let container = _this.findPosition(document.getElementById(_this.containerId));
                let containerLeft = container[0];
                let containerTop = container[1];
                let containerWidth = container[4];
                let containerHeight = container[5];

                /*计算出容器的范围坐标。*/

                //开始 X 坐标。
                let startX = containerLeft;
                //开始 Y 坐标。
                let startY = containerTop;
                //结束 X 坐标。
                let maxX = startX + containerWidth - width;
                //结束 Y 坐标。
                let maxY = startY + containerHeight - height;

                //鼠标选中的元素设置成顶层。
                evnt.target.parentNode.style.zIndex = _this.getMaxIndex() + 1;

                document.onmousemove = function () { };
                document.onmousemove = function (doc) {

                    if (_this.isMouseMove) {
                        //计算出移动后的坐标。
                        let moveLeft = doc.clientX - cleft;
                        let moveTop = doc.clientY - ctop;

                        //设置成绝对定位，让元素可以移动。
                        evnt.target.parentNode.style.position = "absolute";

                        //不可以超出指定的范围。
                        if (moveLeft >= startX && moveTop >= startY && moveLeft <= maxX && moveTop <= maxY) {
                            //当移动位置在范围内时，元素跟随鼠标移动。
                            evnt.target.parentNode.style.left = moveLeft + "px";
                            evnt.target.parentNode.style.top = moveTop + "px";
                        } else {
                            /****************以下为处理当鼠标的位置不在范围内里，鼠标的移动，里面的元素也要跟着移动*****************/
                            //向右移动时，如果移动坐标没有大于最大 X 坐标，则移动，否则设置成最大 X 坐标的值。
                            if (moveLeft >= startX && moveLeft <= maxX) {
                                evnt.target.parentNode.style.left = moveLeft + "px";
                            } else if (moveLeft > maxX) {
                                evnt.target.parentNode.style.left = maxX + "px";
                            } else if (moveLeft < startX) {
                                evnt.target.parentNode.style.left = startX + "px";
                            }


                            //向下移动时，如果移动坐标没有大于最大 Y 坐标，则移动，否则设置成最大 Y 坐标的值。
                            if (moveTop >= startY && moveTop <= maxY) {
                                evnt.target.parentNode.style.top = moveTop + "px";
                            } else if (moveTop > maxY) {
                                evnt.target.parentNode.style.top = maxY + "px";
                            } else if (moveTop < startY) {
                                evnt.target.parentNode.style.top = startY + "px";
                            }
                        }

                        if (0 > moveLeft) {
                            moveLeft = 0;
                        }
                        if (0 > moveTop) {
                            moveTop = 0;
                        }
                        _this.showPosition(evnt, 2, moveLeft, moveTop, evnt.target.parentNode.clientWidth, evnt.target.parentNode.clientHeight, omitItem);
                    }
                }

                document.onmouseup = function () {
                    _this.isMouseMove = false;
                    document.onmousemove = function () { }
                }
            },

            // 缩放
            zoomBind: function (evnt, omitItem) {
                let _this = this;
                _this.isMouseZoom = true;

                //获得容器最大宽度。
                let container = _this.findPosition(document.getElementById(_this.containerId));
                let maxWidth = container[2];
                let maxHeight = container[3];

                //设置最小宽度。
                let minWidth = 50 / 2;
                let minHeight = 30 / 2;

                //获得元素对象。
                let parent = evnt.currentTarget.parentNode;

                //获得当前鼠标在页面的位置。
                let mouseX = evnt.clientX;
                let mouseY = evnt.clientY;

                //获得元素的宽与高。
                let parentSize = _this.findPosition(parent);
                let parentWidth = parentSize[4];
                let parentHeight = parentSize[5];

                parent.onmousedown = function () { };
                document.onmousemove = function (doc) {
                    if (_this.isMouseZoom) {
                        //设置成绝对定位，让元素可以移动。
                        parent.style.position = "absolute";

                        //获得鼠标移动后的页面坐标。
                        let currentMouseX = doc.clientX;
                        let currentMouseY = doc.clientY;

						//获得元素在容器的坐标
						let containerSize = _this.findPosition(parent);
						let containerX = containerSize[2];
						let containerY = containerSize[3];
						// let widthOne = evnt.currentTarget.offsetLeft;
						// let topOne = evnt.currentTarget.offsetTop;


                        // //计算出移动的坐标。
                        let moveWidth = currentMouseX - mouseX;
                        let moveHeight = currentMouseY - mouseY;

						//计算宽度增加或减少的值，增加的量等于鼠标移位的量。
						// let width = parent.offsetWidth + moveWidth;
						// let height = parent.offsetHeight + moveHeight;
						let width, height;
						if (0 > moveWidth) {
							width = parent.offsetWidth + moveWidth;
							if (width > minWidth && maxWidth < containerX) {
								parent.style.width = width + "px";
							}
                        }
                        if (maxWidth >= containerX) {
							width = parent.offsetWidth + moveWidth;
                            if (width > minWidth) {
                                //以元素左下角为定点，则左下角的 Y 坐标 = 当前元素的左上角的 Y 坐标 - （改变后的高度 - 改变前的高度）
                                parent.style.width = width + "px";
                                mouseX = currentMouseX;
                                parentWidth = parent.offsetWidth;
                            }
                        } else {
                        	if (0 === containerSize[0]) {
								width = maxWidth;
                            } else {
								width = parent.offsetWidth;
                            }
                        }
						if (minWidth >= width) {
							width = minWidth;
						}
						if (0 > moveHeight) {
							height = parent.offsetHeight + moveHeight;
							if (height > minHeight && maxHeight < containerY) {
								parent.style.height = height + "px";
							}
						}
                        if (maxHeight >= containerY) {
							height = parent.offsetHeight + moveHeight;
                            if (height > minHeight) {
                                parent.style.height = height + "px";
                                mouseY = currentMouseY;
                                parentHeight = parent.offsetHeight;
                            }
                        } else {
							if (0 === containerSize[1]) {
								height = maxHeight;
							} else {
								height = parent.offsetHeight;
							}
                        }
                        if (minHeight >= height) {
                            height = minHeight
                        }
                        _this.showPosition(evnt, 1, parent.offsetLeft, parent.offsetTop, width, height, omitItem)
                    }
                }

                document.onmouseup = function () {
                    // 缩放的抬起释放
                    _this.isMouseZoom = false;
                    // 清除缩放
                    document.onmousemove = function () {
                    }
                    // 绑定移动
                    parent.onmousedown = function (e) {
                        _this.moveBind(e, omitItem);
                    }
                }
            },

			// 添加
            addElement: function () {
                this.isSubject = true;
                this.isBind = false;
            },

			// 返回
            returnElement: function () {
                this.isSubject = false;
                this.isBind = true;
            },

            // 移除
            zoomDelete: function (evnt, item) {
            	for (let index = 0; index < this.domCount.length; index++) {
					if (this.domCount[index].subjectId === item.subjectId && this.domCount[index].propertyId === item.propertyId) {
						this.domCount.splice(index, 1);
						break;
					}
                }
            },

			// 开始移动
            moveStart: function (evnt) {
                if (evnt.target.className != 'resize') {
                    evnt.target.style.cursor='move';
                }
            },

            //获得最大 Z 坐标
            getMaxIndex: function () {
                let index = 0;
                let ds = document.getElementById(this.containerId).getElementsByTagName(this.moveElement);
                let length = document.getElementById(this.containerId).getElementsByTagName(this.moveElement).length;
                for (let loop = 0; loop < length; loop++) {
                    if (ds[loop].style.zIndex > index) index = ds[loop].style.zIndex;
                }
                return parseInt(index);
            },

            //获得元素的坐标与大小
            findPosition: function (oElement) {
                if (this.isBind) {
                    let x2 = 0;
                    let y2 = 0;
                    let width = oElement.clientWidth;
                    let height = oElement.clientHeight;
                    if (typeof (oElement.offsetParent) != 'undefined') {
                        // let posX = 0, posY = 0;
                        // for ( oElement; oElement = oElement.offsetParent;) {
                        //     posX += oElement.offsetLeft;
                        //     posY += oElement.offsetTop;
                        // }
                        let posX = oElement.offsetLeft;
                        let posY = oElement.offsetTop;
                        x2 = posX + width;
                        y2 = posY + height;
                        return [posX, posY, x2, y2, width, height];

                    } else {
                        x2 = oElement.x + width;
                        y2 = oElement.y + height;
                        // 右上角的x，y，右下角的x，y
                        return [oElement.x, oElement.y, x2, y2, width, height];
                    }
                }
            },

            getImgAttributes: function () {
            	if (!this.isSize) {
					let imgAttributes = document.getElementsByClassName('card-main-img');
					this.baseWidth = imgAttributes[0].clientWidth / 2;
					this.baseHeight = imgAttributes[0].clientHeight / 2;
					this.maskHeight = imgAttributes[0].clientHeight / 2;
					this.isSize = true;
                }
            },

            getChecked: function () {
                if ($('#isShow').is(':checked') != this.isShow) {
                    this.isShow = $('#isShow').is(':checked');
                }
            },

            //显示坐标信息
            showPosition: function (evnt, type, x, y, w, h, omitItem) {
                // 获取屏幕DPI
                let dpi = this.conversion_getDPI()[0];
                let x1 = x / dpi * 25.4;
                let y1 = y / dpi * 25.4;
                let w1 = w / dpi * 25.4;
                let h1 = h / dpi * 25.4;

                // 转换前把像素位置存起来
                omitItem.omitXpx = x;
                omitItem.omitYpx = y;
                omitItem.omitWpx = w;
                omitItem.omitHpx = h;

                // 根据dpi计算屏幕和A4的比例
                let smm1 = this.baseWidth / dpi * 25.4;
                let amm = 210;
                let proportion1 = smm1 / amm;
                x = x1 / proportion1;
                y = y1 / proportion1;
                w = w1 / proportion1;
                h = h1 / proportion1;
                // console.log(lastX1);
                // console.log('---');

                // // 获取每毫米的像素值(1mm有多少px)
                // let mm = this.unitConversion();
                // let x2 = x / mm;
                // console.log(x2);
                // // 根据转换关系计算屏幕和A4的比例
                // let smm2 = this.baseWidth / mm;
                // let proportion2 = smm2 / amm;
                // let lastX2 = x2 / proportion2;
                // console.log(lastX2);
                // console.log('+++');

                if (type === 1) {
                    let omitName = evnt.target.parentNode.children[1].getAttribute("omitName");
                    let answerOrder = evnt.target.parentNode.children[1].getAttribute("answerOrder");
                    if (1 === omitItem.tagType) {
                        evnt.target.parentNode.children[1].innerHTML = x + "," + y + "<br/>" + w + "," + h + "<div class='omit-name'>" + omitName + "（切题）</div>";
                    } else if (2 === omitItem.tagType) {
                        evnt.target.parentNode.children[1].innerHTML = x + "," + y + "<br/>" + w + "," + h + "<div class='omit-name'>" + omitName + "（答题" + answerOrder + "）</div>";
                    }
                    omitItem.xaxis = x;
                    omitItem.yaxis = y;
                    omitItem.width = w;
                    omitItem.height = h;
                } else {
                    let omitName = evnt.target.parentNode.children[1].getAttribute("omitName");
                    let answerOrder = evnt.target.parentNode.children[1].getAttribute("answerOrder");
                    if (1 === omitItem.tagType) {
                        evnt.target.parentNode.children[1].innerHTML = x + "," + y + "<br/>" + w + "," + h + "<div class='omit-name'>" + omitName + "（切题）</div>"
                    } else if (2 === omitItem.tagType) {
                        evnt.target.parentNode.children[1].innerHTML = x + "," + y + "<br/>" + w + "," + h + "<div class='omit-name'>" + omitName + "（答题" + answerOrder + "）</div>"
                    }
                    omitItem.xaxis = x;
                    omitItem.yaxis = y;
                    omitItem.width = w;
                    omitItem.height = h;
                }
            },

            // 获取DPI
            conversion_getDPI: function () {
                var arrDPI = new Array;
                if (window.screen.deviceXDPI) {
                    arrDPI[0] = window.screen.deviceXDPI;
                    arrDPI[1] = window.screen.deviceYDPI;
                } else {
                    var tmpNode = document.createElement("DIV");
                    tmpNode.style.cssText = "width:1in;height:1in;position:absolute;left:0px;top:0px;z-index:99;visibility:hidden";
                    document.body.appendChild(tmpNode);
                    arrDPI[0] = parseInt(tmpNode.offsetWidth);
                    arrDPI[1] = parseInt(tmpNode.offsetHeight);
                    tmpNode.parentNode.removeChild(tmpNode);
                }
                return arrDPI;
            },

            // // 获取每毫米的像素值
            // unitConversion: function () {
            //     // 创建一个1mm宽的元素插入到页面，然后坐等出结果
            //     let div = document.createElement("div");
            //     div.id = "mm";
            //     div.style.width = "1mm";
            //     // div.style.display = "none";
            //     document.querySelector("body").appendChild(div);
            //     // 原生方法获取浏览器对元素的计算值
            //     let mm1 = document.getElementById("mm").getBoundingClientRect();
            //     console.log(mm1);
            //     return mm1.width;
            // }
        }
    }
</script>

<style scoped>
    .custom-radio {
        cursor: pointer;
    }
    .cursor-not {
        cursor: not-allowed !important;
    }
    .card-main {
        position: relative;
        overflow: auto;
        padding: 0;
    }
    .card-main-content {
        position: absolute;
        left: 0;
        top: 0;
    }
    .card-main-img {

    }
    .img-size {
        width: 100%;
    }
    #mainContainer {
        /*border: 1px solid #990000;*/
        position: absolute;
        left: 0;
        top: 0;
        z-index: 9;
    }

    #innerContainer {
        width: 100%;
        height: 100%;
        /*background-color: #000000;*/
        /*opacity: 0.3;*/
    }

    .omit-name {
        text-overflow: ellipsis;
        white-space:nowrap;
        overflow:hidden;
    }
</style>