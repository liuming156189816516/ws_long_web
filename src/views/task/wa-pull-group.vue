<template>
    <div>
        <el-form size="small" :inline="true" style="margin-top: 10px;">
          <el-form-item>
            <el-button type="primary" @click="jumpCreatTask(0,0)">{{ $t('sys_m038') }}</el-button>
          </el-form-item>
          <el-form-item>
            <el-button type="warning" :disabled="checkIdArry.length==0" @click="handleGroupBtn(1)">{{ $t('sys_rai081') }}</el-button>
          </el-form-item>
          <el-form-item>
            <el-button type="warning" :disabled="checkIdArry.length==0" @click="handleGroupBtn(3)">{{ $t('sys_rai097') }}</el-button>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" :disabled="checkIdArry.length==0" @click="handleGroupBtn(4)">{{ $t('sys_q111') }}</el-button>
          </el-form-item>
          <el-form-item>
            <el-button type="danger" :disabled="checkIdArry.length==0" @click="handleGroupBtn(2)">{{ $t('sys_rai082') }}</el-button>
          </el-form-item>
          <el-form-item>
            <el-input clearable v-model="model1.task_name" :placeholder="$t('sys_mat024')"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" icon="el-icon-search" @click="getPullTaskList(1)">{{ $t('sys_c002') }}</el-button>
            <el-button icon="el-icon-refresh-right" @click="resetQuery">{{ $t('sys_c049') }}</el-button>
          </el-form-item>

          <el-form-item class="fr">
            自动炸群: <el-switch v-model="auto_scamper" active-text="开启" inactive-text="关闭" @change="handleScamper" />
          </el-form-item>
        </el-form>
        <!-- 分组管理 -->
        <div class="continer_main">
          <div class="tab_check_warp">
              <i slot="reference" class="el-icon-info"></i>
              <div v-html="$t('sys_mat007',{value:checkIdArry.length})"></div>
          </div>
          <!-- <el-table @sort-change="sorthandle" :summary-method="getSummaries" show-summary :data="taskDataList" row-key="id" use-virtual border height="750" v-loading="loading" ref="serveTable"
            element-loading-spinner="el-icon-loading" style="width: 100%;" showBodyOverflow="title" :total="model1.total" :page-sizes="pageOption" 
            :page-size="model1.limit" :current-page="model1.page" :pagination-show="true" @selection-change="handleSelectionChange" @row-click="rowSelectChange" @handlePageSize="pageHandle">    -->
          
            <el-table :summary-method="getSummaries" show-summary :data="taskDataList" row-key="id" use-virtual
              border height="700" v-loading="loading" ref="serveTable" element-loading-spinner="el-icon-loading" style="width: 100%;"
              @selection-change="handleSelectionChange" @row-click="rowSelectChange">  
              <el-table-column type="selection" width="55" />
              <el-table-column prop="name" :label="$t('sys_g070')" width="120" />
              <el-table-column prop="pull_group_name" :label="$t('sys_rai088')" minWidth="120" />
              <el-table-column prop="ad_group_name" :label="$t('sys_rai089')" minWidth="120" />
              <el-table-column prop="data_pack_name" :label="$t('sys_rai090')" width="120" />
              <!-- <el-table-column prop="ad" show-overflow-tooltip :label="$t('sys_rai091')" minWidth="120" /> -->
              <el-table-column prop="zq_num" :label="$t('sys_rai095')" minWidth="100" />
              <el-table-column prop="is_announcement" :label="$t('sys_q131')" minWidth="100">
                <template slot-scope="scope">
                  <el-button class="jump_un_link"type="text" :disabled="!scope.row.material_list" @click.stop="scamperBtn(scope.row,1)">{{ scope.row.material_list==null?0:scope.row.material_list.length }}</el-button>
                </template>
              </el-table-column>
              <el-table-column prop="qnum" :label="$t('sys_rai092')" width="120" />
              <el-table-column prop="pull_num" :label="$t('sys_rai093')" width="120" />
              <el-table-column prop="avg_num" :label="$t('sys_rai099')" width="120" />
              <el-table-column prop="success_num" :label="$t('sys_rai094')" width="120" />
              <el-table-column prop="status" :label="$t('sys_rai001')" width="100">
                <template slot="header">
                  <el-dropdown trigger="click" size="medium " @command="(command) => handleNewwork(command)">
                    <span style="color:#909399" :class="[model1.status?'dropdown_title':'']"> {{ $t('sys_l059') }}
                        <i class="el-icon-arrow-down el-icon--right" />
                    </span>
                    <el-dropdown-menu slot="dropdown">
                        <el-dropdown-item :class="{'dropdown_selected':idx==model1.status}" v-for="(item,idx) in statusOptions" :key="idx" :command="idx">{{ item==''?$t('sys_l053'):item }}</el-dropdown-item>
                    </el-dropdown-menu>
                    </el-dropdown>
                </template>
                <template slot-scope="scope">
                  <el-tag size="small" :type="scope.row.status==1?'info':scope.row.status==2?'warning':scope.row.status==5?'success':'danger'"> {{ statusOptions[scope.row.status]||"-" }}</el-tag>
                </template>
              </el-table-column>
              <el-table-column prop="itime" :label="$t('sys_c008')" minWidth="160">
                  <template slot-scope="scope">
                    <div>{{scope.row.itime>0?$baseFun.resetTime(scope.row.itime*1000):"-" }}</div>
                  </template>
              </el-table-column>
              <el-table-column fixed="right" :label="$t('sys_c010')" width="180">
                  <template slot-scope="scope">
                    <!-- <el-button :disabled="checkIdArry.length>0" type="primary" size="mini" @click.stop="exportText(scope.row)">{{ $t('sys_rai079') }}</el-button> -->
                    <el-button :disabled="checkIdArry.length>0" type="primary" plain size="mini" @click.stop="beforeOneBtn(scope.row)">{{ $t('sys_rai098') }}</el-button>
                    <el-button :disabled="checkIdArry.length>0" type="success" plain size="mini" @click.stop="goTaskDetail(scope.row)">{{ $t('sys_rai080') }}</el-button>
                  </template>
              </el-table-column>
          </el-table>
          <div class="layui_page">
              <el-pagination background :page-sizes="[10, 20, 50, 100, 200, 500, 1000]" :current-page.sync="model1.page" 
              :page-size="model1.limit" layout="total, sizes, prev, pager, next, jumper" :total="model1.total"  
              @size-change="handleSize" @current-change="handlePage" />
          </div>
          <!-- <el-pagination :total="model1.total" style="display: none;" /> -->
        </div>
        <el-dialog :title="taskForm.relpy_type==2?$t('sys_rai098'):$t('sys_q131')" :visible.sync="dialogVisible" width="560px" center>
          <template v-if="taskForm.relpy_type==2">
            <el-form :model="taskForm" size="small" :rules="taskRules" ref="taskForm" label-width="100px" class="demo-ruleForm">
              <el-form-item :label="$t('sys_rai104')+'：'" prop="relpy_text">
                <el-input type="textarea" clearable v-model="taskForm.relpy_text" :placeholder="$t('sys_g129')" rows="8" />
              </el-form-item>
              <el-form-item>
                <div class="el-item-right">
                  <el-button @click="dialogVisible=false">{{ $t('sys_c023') }}</el-button>
                  <el-button type="primary" :loading="isLoading" @click="submitForm('taskForm')">{{ $t('sys_c024') }}</el-button>
                </div>
              </el-form-item>
            </el-form>
          </template>
          <template v-else>
            <el-table :data="materialData" :header-cell-style="{ color: '#909399', textAlign: 'center' }" :cell-style="{ textAlign: 'center' }" style="width: 100%">
                <el-table-column type="index" :label="$t('sys_g020')"></el-table-column>
                <el-table-column prop="type" :label="$t('sys_g091')" minWidth="120">
                    <template slot-scope="scope">
                        <span>{{ sourceOption[scope.row.type]}}</span>
                    </template>
                </el-table-column>
                <el-table-column prop="content" :label="$t('sys_mat019')" minWidth="100">
                    <template slot-scope="scope">
                        <span class="content_01" v-if="scope.row.type==1||scope.row.type==5||scope.row.type==6||scope.row.type==7">{{ scope.row.content }}</span>
                        <div v-if="scope.row.type==2" style="display: flex;flex-direction: column;justify-content: center;align-items: center;">
                          <img class="content_02" :src="scope.row.content" @click="showSkyBtn(scope.row)">
                          <span style="display: flex;line-height: 1;margin-top: 3px;font-size: 12px;">{{ scope.row.remark }}</span>
                        </div>
                        <audio v-if="scope.row.type==3" controls class="audio_src">
                          <source :src="scope.row.content" type="audio/mpeg">
                        </audio>
                        <video v-if="scope.row.type==4" width="60" height="35" controls>
                          <source :src="scope.row.content" type="video/mp4">
                        </video>
                    </template>
                </el-table-column>
                <!-- <el-table-column prop="address" :label="$t('sys_c010')" width="120">
                    <template slot-scope="scope">
                        <el-button class="custom_btn" size="mini" v-if="scope.row.type!=5" @click="editScript(scope.row,scope)">
                            <i class="el-icon-edit" />
                        </el-button>
                        <el-button class="custom_btn" size="mini" @click="delScript(scope)">
                            <i class="el-icon-delete-solid" />
                        </el-button>
                    </template>
                </el-table-column> -->
            </el-table>
          </template>
        </el-dialog>
        <el-image-viewer v-if="imgModel" :on-close="closeViewer" style="z-index:9999" @click.native="cloneImgpreview" :url-list="[taskForm.img]" />
    </div>
  </template>
  <script>
  import { successTips } from '@/utils/index'
  import { groupsendmsg,getpullgrouptasklist,dobatchdelpullgrouptask,dobatchstoppullgrouptask,dobatchclosepullgrouptask,getsysconfig,upsysconfig} from '@/api/task'
  export default {
    components: {'el-image-viewer': () => import('element-ui/packages/image/src/image-viewer') },
    data() {
      return {
        model1: {
          page: 1,
          limit: 100,
          total: 0,
          status:"",
          task_name: "",
        },
        taskForm:{
          img:"",
          relpy_type:"",
          relpy_id:"",
          relpy_text:"",
        },
        imgModel:false,
        loading:false,
        isLoading:false,
        auto_scamper:false,
        checkIdArry:[],
        taskDataList:[],
        showNum:[11],
        materialData:[],
        dialogVisible:false
      }
    },
    computed: {
      taskRules() {
        return {
          relpy_text: [{ required: true, message: this.$t('sys_mat021'), trigger: 'blure' },{ max: 2000, message: '最多可输入2000个字符', trigger: 'blur' }],
        }
      },
      groupRules() {
        return {
          group_id: [{ required: true, message: this.$t('sys_c052'), trigger: 'change' }],
          plant_type: [{ required: true, message: this.$t('sys_c052'), trigger: 'blure' }],
          set_account: [{ required: true, message: this.$t('sys_mat061',{value:this.$t('sys_mat063')}), trigger: 'blure' }],
          set_name: [{ required: true, message: this.$t('sys_mat061',{value:this.$t('sys_mat062')}), trigger: 'blure' }],
          set_pwd: [{ required: true, message: this.$t('sys_mat061',{value:this.$t('sys_g031')}), trigger: 'blure' }]
        }
      },
      raisOptions(){
        return ["",this.$t('sys_rai002'),this.$t('sys_rai003'),this.$t('sys_rai004')]
      },
      statusOptions(){
        return ["",this.$t('sys_l071'),this.$t('sys_l072'),this.$t('sys_g068'),this.$t('sys_rai078'),this.$t('sys_mat047')]
      },
      sourceOption() {
          return ["",this.$t('sys_mat008'),this.$t('sys_mat009'),this.$t('sys_mat010'),this.$t('sys_mat011'),this.$t('sys_mat091'),this.$t('sys_mat092')]
      }
    },
    created() {
      this.initGroupConfig();
      this.getPullTaskList();
    },
    methods: {
        resetQuery(){
          this.model1.status="";
          this.model1.task_name="";
          this.getPullTaskList(1);
          this.$refs.serveTable.clearSelection();
        },
        handleNewwork(status) {
          this.model1.status = status;
          this.getPullTaskList(1);
        },
        scamperBtn(row,type){
          this.taskForm.relpy_type=type;
          this.taskForm.relpy_id=row.id;
          this.taskForm.relpy_text=row.ad;
          this.materialData = row.material_list||[];
          this.dialogVisible=true;
        },
        getPullTaskList(num){
          this.loading=true;
          this.taskDataList=[];
          this.model1.page=num?num:this.model1.page;
          let params = {
            page:this.model1.page,
            limit:this.model1.limit,
            name:this.model1.task_name,
            status:this.model1.status||-1
          }
          getpullgrouptasklist(params).then(res=>{
            this.loading=false;
            this.model1.total = res.data.total;
            this.taskDataList = res.data.list||[];
            this.$nextTick(()=>{
              if (this.$refs.serveTable) {
                this.$refs.serveTable.doLayout(); 
              }
            })
          })
        },
        getSummaries(param) {
          const { columns, data } = param;
          const sums = [];
          columns.forEach((column, index) => {
            const values = data.map(item => Number(item[column.property]));
            if (index === 0) {
              sums[index] = this.$t('sys_c125');
              return;
            }else if(this.showNum.indexOf(index) > -1){
              sums[index] = values.reduce((prev, curr) => {
                const value = Number(curr);
                if (!isNaN(value)) {
                  return prev+curr;
                } else {
                  return prev;
                }
              },0);
            }else{
              sums[index] = '--';	
            }
          });
          return sums;
        },
        async initGroupConfig(){
          const {data:{auto_pull_group}} = await getsysconfig();
          this.auto_scamper = auto_pull_group==1?true:false;
        },
        async handleScamper(e){
          const {code} = await upsysconfig({auto_pull_group:this.auto_scamper?1:0});
          if (code !=0 ) return;
          this.auto_scamper = e;
          successTips(this)
        },
        goTaskDetail(row){
          this.$router.push({path:'/wa-group-detail',query:{params:row,task_id:row.id}});
        },
        submitForm(formName){
          this.$refs[formName].validate((valid) => {
          if (valid) {
            this.isLoading=true;
            let ids = this.taskForm.relpy_type==1?this.checkIdArry:[this.taskForm.relpy_id];
            groupsendmsg({ids:ids,ad:this.taskForm.relpy_text}).then(res=>{
              this.isLoading=false;
              if (res.code !=0 ) return;
              successTips(this)
              this.getPullTaskList(1);
              this.dialogVisible=false;
            })
          } else {
            console.log('error submit!!');
            return false;
          }
        })
      },
      beforeOneBtn(row){
        let that = this;
        that.$confirm(that.$t('sys_rai046',{value:that.$t('sys_rai098')}), that.$t('sys_l013'), {
            type: 'warning',
            confirmButtonText: that.$t('sys_c024'),
            cancelButtonText: that.$t('sys_c023'),
            beforeClose: function (action, instance, done) {
                if (action === 'confirm') {
                  instance.confirmButtonLoading = true;
                  groupsendmsg({ids:[row.id]}).then(res=>{
                    instance.confirmButtonLoading = false;
                    if (res.code != 0) return;
                      that.getPullTaskList(1);
                      successTips(that)
                      done();
                    })
                } else {
                  done();
                }
            }
        }).catch(() => {
          that.$message({ type: 'info', message: that.$t('sys_c048') });
        })
      },
        handleSelectionChange(row) {
          this.checkIdArry = row.map(item => { return item.id })
        },
        rowSelectChange(row, column, event) {
            let refsElTable = this.$refs.serveTable;
            let findRow = this.checkIdArry.find(item => item == row.id);
            if (findRow) {
              refsElTable.toggleRowSelection(row, false);
              return;
            }
            refsElTable.toggleRowSelection(row,true);
        },
        // rowSelectChange(row) {
        //   let tableCell = this.$refs.serveTable;
        //   if (this.checkIdArry.includes(row.id)) {
        //     tableCell.toggleRowSelection([{row:row,selected:false}]);
        //     return;
        //   }
        //   tableCell.toggleRowSelection([{row:row,selected:true}]);
        // },
        sorthandle({ column, prop, order }) {
          console.log(column);
        },
        sizeHandle(val){
          this.model1.limit=val;
          this.getPullTaskList();
        },
        handleSize(size){
          this.model1.limit = size;
          this.getPullTaskList(1);
        },
        handlePage(page){
          this.model1.page = page;
          this.getPullTaskList();
        },
        // pageHandle({page, size}){
        //   this.loading = true;
        //   if (this.model1.limit != size) {
        //     this.model1.page = 1;
        //   }else{
        //     this.model1.page = page;
        //   }
        //   this.model1.limit = size;
        //   this.getPullTaskList();
        // },
        jumpCreatTask(row,idx){
          this.$router.push({path:"/wa-add-group",query:{id:5555}})
        },
        handleGroupBtn(type) {
            let that = this;
            let tipsText = type==1?that.$t('sys_rai081'):type==2?that.$t('sys_rai082'):type==2?that.$t('sys_rai097'):that.$t('sys_q111')
            that.$confirm(that.$t('sys_rai046',{value:tipsText}), that.$t('sys_l013'), {
              type: 'warning',
              confirmButtonText: that.$t('sys_c024'),
              cancelButtonText: that.$t('sys_c023'),
              beforeClose: function (action, instance, done) {
                  if (action === 'confirm') {
                    let reqApi = type==1?dobatchclosepullgrouptask:type==2?dobatchdelpullgrouptask:type==3?dobatchstoppullgrouptask:groupsendmsg;
                    instance.confirmButtonLoading = true;
                    reqApi({ids:that.checkIdArry}).then(res=>{
                      instance.confirmButtonLoading = false;
                      if (res.code != 0) return;
                      that.getPullTaskList(1);
                      successTips(that)
                      done();
                    })
                  } else {
                    done();
                  }
              }
          }).catch(() => {
              that.$message({ type: 'info', message: that.$t('sys_c048') });
          })
        },
        showSkyBtn(row){
          this.taskForm.img=row.content;
          this.imgModel = true;
        },
        closeViewer(e) {
          this.imgModel = false;
        },
        cloneImgpreview(e) {
          if (e.target.getAttribute('class') === 'el-image-viewer__mask') {
            this.imgModel = false;
          }
        },
        exportText(row){
          doouttaskexcel({id:row.id}).then(res=>{
            if (res.code != 0) return;
              this.getPullTaskList(1);
              window.location.href = res.data.url
          })
        }
    }
  }
  </script>
  <style scoped lang="scss">
  .continer_main {
    width: 100%;
  }
  .content_01{
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }
  .audio_src{
    width: 140px;
    height: 30px;
  }
  .content_02{
    width: 44px;
    height: 28px;
  }
  </style>
    