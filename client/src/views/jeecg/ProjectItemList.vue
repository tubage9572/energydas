<template>
  <a-row :gutter="10">
    <a-col :md="8" :sm="24">
      <a-card :bordered="false">
        <div style="background: #fff;padding-left:16px;height: 100%; margin-top: 5px">
          <a-input-search @search="onSearch" style="width:100%;margin-top: 10px" placeholder="请输入"/>
          <!-- 树-->

          <template>

            <!--组织机构-->
              <a-directory-tree
              selectable
              :selectedKeys="selectedKeys"
              :checkStrictly="true"
              @select="onSelect"
              :dropdownStyle="{maxHeight:'200px',overflow:'auto'}"
              :treeData="tbProjectItem"
            />

          </template>

        </div>
      </a-card>
    </a-col>
    <!-- a-col :md="16" :sm="24">
      <a-card :bordered="false">
        <a-tabs defaultActiveKey="2" @change="callback">
          <a-tab-pane tab="基本信息" key="1" forceRender>
            <Dept-Base-Info ref="DeptBaseInfo"></Dept-Base-Info>
          </a-tab-pane>
          <a-tab-pane tab="用户信息" key="2">
            <Dept-User-Info ref="DeptUserInfo"></Dept-User-Info>
          </a-tab-pane>
        </a-tabs>
      </a-card>
    </a-col -->
  </a-row>
</template>
<script>
 //== import DeptBaseInfo from './modules/DeptBaseInfo'
 //== import DeptUserInfo from './modules/DeptUserInfo'
  import {queryTbProjectItemList, searcTbProjectItemhByKeywords} from '@/api/api'
  import {JeecgListMixin} from '@/mixins/JeecgListMixin'

  export default {
    name: 'TbProjectItemList',
    mixins: [JeecgListMixin],
    /*components: {
      DeptBaseInfo,
      DeptUserInfo,
    },*/
    data() {
      return {
        currentProjectId: '',
        iExpandedKeys: [],
        loading: false,
        autoExpandParent: true,
        currFlowId: '',
        currFlowName: '',
        disable: true,
        treeData: [],
        visible: false,
        tbProjectItem: [],
        rightClickSelectedKey: '',
        hiding: true,
        model: {},
        dropTrigger: '',
        tbProject: {},
        disableSubmit: false,
        checkedKeys: [],
        selectedKeys: [],
        autoIncr: 1,
        currSelected: {},
        form: this.$form.createForm(this),
        labelCol: {
          xs: {span: 24},
          sm: {span: 5}
        },
        wrapperCol: {
          xs: {span: 24},
          sm: {span: 16}
        },
        graphDatasource: {
          nodes: [],
          edges: []
        },
      }
    },
    methods: {
      callback(key) {
        console.log(key)
      },
      loadData() {
        this.refresh();
      },
      loadTree() {
        var that = this
        that.treeData = []
        that.tbProjectItem = []
        queryTbProjectItemList().then((res) => {
          if (res.success) {
            for (let i = 0; i < res.result.length; i++) {
              let temp = res.result[i]
              that.treeData.push(temp)
              that.tbProjectItem.push(temp)
              that.setThisExpandedKeys(temp)
              // console.log(temp.id)
            }
            this.loading = false
          }
        })
      },
      setThisExpandedKeys(node) {
        if (node.children && node.children.length > 0) {
          this.iExpandedKeys.push(node.key)
          for (let a = 0; a < node.children.length; a++) {
            this.setThisExpandedKeys(node.children[a])
          }
        }
      },
      refresh() {
        this.loading = true
        this.loadTree()
      },

      onExpand(expandedKeys) {
        console.log('onExpand', expandedKeys)
        // if not set autoExpandParent to false, if children expanded, parent can not collapse.
        // or, you can remove all expanded children keys.
        this.iExpandedKeys = expandedKeys
        this.autoExpandParent = false

      },

      onSearch(value) {
        let that = this
        if (value) {
          alert(0);

          searcTbProjectItemhByKeywords({keyWord: value}).then((res) => {
            if (res.success) {

              that.tbProjectItem = []
              for (let i = 0; i < res.result.length; i++) {
                let temp = res.result[i]
                that.tbProjectItem.push(temp)
              }
            } else {
              that.$message.warning(res.message)
            }
          })
        } else {
          that.loadTree()
        }

      },
      onCheck(checkedKeys, e) {
        let record = e.node.dataRef;
        // console.log('onCheck', checkedKeys, e);
        this.checkedKeys = [];
        // if (e.checked === true) {
        this.currentProjectId = record.id;
        this.checkedKeys.push(record.id);

        //==this.$refs.DeptBaseInfo.open(record);
        //==this.$refs.DeptUserInfo.open(record);
        // }
        // else {
        //   this.checkedKeys = [];
        //   this.$refs.DeptBaseInfo.clearForm();
        //   this.$refs.DeptUserInfo.clearList();
        // }

        this.hiding = false;
        // this.checkedKeys = checkedKeys.checked
      },
      onSelect(selectedKeys, e) {
        if (this.selectedKeys[0] !== selectedKeys[0]) {
          this.selectedKeys = [selectedKeys[0]];
        }
        let record = e.node.dataRef;
        this.checkedKeys.push(record.id);
        //==this.$refs.DeptBaseInfo.open(record);
        //==this.$refs.DeptUserInfo.onClearSelected();
        //==this.$refs.DeptUserInfo.open(record);
      },
    },
    created() {
      this.currFlowId = this.$route.params.id
      this.currFlowName = this.$route.params.name
      // this.loadTree()
    },
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>