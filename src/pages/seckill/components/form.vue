<template>
  <div class="add">
    <el-dialog :title="info.title" :visible.sync="info.isshow" @closed="closed">
      <el-form :model="user">
        <el-form-item label="活动名称" label-width="120px">
          <el-input v-model="user.title" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="活动期限" label-width="120px">
          <el-time-picker
            is-range
            v-model="user.value"
            range-separator="至"
            start-placeholder="开始时间"
            end-placeholder="结束时间"
            placeholder="选择时间范围"
            editable
          ></el-time-picker>

          <!-- <el-input v-model="user.catename" autocomplete="off"></el-input> -->
        </el-form-item>
        <el-form-item label="一级分类" label-width="120px">
          <el-select v-model="user.first_cateid" placeholder="请选择" @change="changeFirst">
            <!-- <el-option :value="0" label="顶级菜单"></el-option> -->
            <el-option
              v-for="item in goodslist"
              :key="item.id"
              :value="item.id"
              :label="item.first_cateid"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="二级分类" label-width="120px">
          <el-select v-model="user.second_cateid" placeholder="请选择">
            <!-- <el-option :value="0" label="顶级菜单"></el-option> -->
            <el-option
              v-for="item in secondCateList"
              :key="item.id"
              :value="item.id"
              :label="item.goodsname"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="商品" label-width="120px">
          <el-select v-model="user.goodsname" placeholder="请选择">
            <!-- <el-option :value="0" label="顶级菜单"></el-option> -->
            <el-option
              v-for="item in goodslist"
              :key="item.id"
              :value="item.id"
              :label="item.goodsname"
            ></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="状态" label-width="120px">
          <el-switch v-model="user.status" :active-value="1" :inactive-value="2"></el-switch>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancel">取 消</el-button>
        <el-button type="primary" v-if="info.title=='添加列表'" @click="add">添 加</el-button>
        <el-button type="primary" v-else @click="update">修 改</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import { mapGetters, mapActions } from "vuex";
import {
  reqgoodsList,
  reqseckAdd,
  reqseckList,
  reqseckDetail,
  reqseckUpdate
} from "../../../utils/http";
import { successAlert } from "../../../utils/alert";
export default {
  props: ["info"],
  data() {
    return {
      user: {
        title: "",
        value: "",
        begintime: "",
        endtime: "",
        first_cateid: "",
        second_cateid: "",
        goodsid: "",
        status: 1
      },
      //二级分类的list
      secondCateList: []
    };
  },
  computed: {
    ...mapGetters({
      goodslist: "goods/list"
    })
  },
  methods: {
    ...mapActions({
      reqgoodsList: "goods/reqList"
    }),
    //6.点了取消
    cancel() {
      this.info.isshow = false;
    },
    //17.清空数据
    empty() {
      this.user = {
        title: "",
        begintime: "",
        endtime: "",
        first_cateid: "",
        second_cateid: "",
        goodsid: "",
        status: 1
      };
    },
    changeFirst() {
      //二级分类的id重置
      this.user.second_cateid = "";
      this.getSecondList();
    },
    getSecondList() {
      //获取二级分类list
      reqgoodsList({ pid: this.user.first_cateid }).then(res => {
        this.secondCateList = res.data.list;
      });
    },

    //10.点了添加按钮
    add() {
      //16.ajax
      reqseckAdd(this.user).then(res => {
        if (res.data.code == 200) {
          //弹成功
          successAlert("添加成功");
          //弹框消失
          this.cancel();
          //数据清空
          this.empty();
          //24 刷新list
          this.reqseckList();
        }
      });
    },
    //37 获取详情
    getOne(id) {
      reqseckDetail(id).then(res => {
        //此刻user没有id
        this.user = res.data.list;
        //补id
        this.user.id = id;
      });
    },
    //39 修改
    update() {
      reqseckUpdate(this.user).then(res => {
        if (res.data.code == 200) {
          //弹成功
          successAlert("修改成功");
          //弹框消失
          this.cancel();
          //数据清空
          this.empty();
          //刷新list
          this.reqseckList();
        }
      });
    },
    //41.处理消失
    closed() {
      if (this.info.title === "编辑列表") {
        this.empty();
      }
    }
  },
  mounted() {
    //   12.一进来，先获取s]商品列表数据
    // this.reqgoodsList()
    reqgoodsList().then(res => {
      if (res.data.code == 200) {
        this.goodsList = res.data.list;
      }
    });
  }
};
</script>
<style scoped>
</style>