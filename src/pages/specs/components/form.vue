<template>
  <div>
    <!-- 4.绑定数据到模板 -->
    <!-- 40 绑定closed -->
    <el-dialog :title="info.title" :visible.sync="info.isshow" @closed="closed">
      <el-form :model="user">
        <el-form-item label="规格名称" label-width="120px">
          <!-- 9.通过v-model将user绑定到表单上 -->
          <el-input v-model="user.specsname" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="规格属性" label-width="120px" v-for="(item,index) in attrArr" :key="index">
          <div class="line">
            <el-input v-model="item.value " autocomplete="off"></el-input>
            <el-button type="primary" v-if="index==0" @click="attrAdd">添加规格属性</el-button>
            <el-button type="danger" v-else @click="attrDel(index)">删除</el-button>
          </div>
        </el-form-item>

        <el-form-item label="状态" label-width="120px">
          <el-switch v-model="user.status" :active-value="1" :inactive-value="2"></el-switch>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancel">取 消</el-button>
        <el-button type="primary" v-if="info.title=='添加规格'" @click="add">添 加</el-button>
        <el-button type="primary" v-else @click="update">修 改</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import { mapGetters, mapActions } from "vuex";
import {
  reqspecsAdd,
  reqspecsDel,
  reqspecsDetail,
  reqspecsUpdate,
 
} from "../../../utils/http";
import { successAlert } from "../../../utils/alert";
export default {
  // 3.接收info
  props: ["info"],
  data() {
    return {
      //7.初始化user
      user: {
        specsname: "",
        attrs: "",
        status: 1
      },

      attrArr: [{ value: "" }]
    };
  },
  computed: {
    ...mapGetters({})
  },
  methods: {
    ...mapActions({
      reqList: "specs/reqList",
      reqCount: "specs/reqCount"
    }),
    cancel() {
      this.info.isshow = false;
    },
    attrAdd() {
      this.attrArr.push({ value: "" });
    },
    attrDel(index) {
      // 从当前下标值截取到一个
      this.attrArr.splice(index, 1);
    },
 
    empty() {
      this.user = {
        specsname: "",
        attrs: "",
        status: 1
      };
      this.attrArr = [{ value: "" }];
    },

    add() {
      this.user.attrs = JSON.stringify(this.attrArr.map(item => item.value));
      reqspecsAdd(this.user).then(res => {
        if (res.data.code == 200) {
          successAlert("添加成功");
          this.cancel();
          this.empty();
          this.reqList();
          this.reqCount();
        }
      });
    },
    getOne(id) {
      reqspecsDetail(id).then(res => {
        this.user = res.data.list[0];
        // map返回一个新的对象
        this.attrArr = JSON.parse(this.user.attrs).map(item => {
          return { value: item };
        });
      });
    },
    update() {
      this.user.attrs = JSON.stringify(this.attrArr.map(item => item.value));
      if (res.data.code == 200) {
        successAlert("更新成功");
        this.reqList();
        this.cancel();
        this.empty();
      }
    },
    closed() {
      if ((this.info.title = "编辑规格")) {
        this.empty();
      }
    }
  },
  mounted() {}
};
</script>
<style scoped>
.line {
  display: flex;
}
.line .el-input {
  flex: 1;
}
.line .el-button {
  width: auto;
}
</style>