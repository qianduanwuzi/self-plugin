<template>
  <self-table
    txt="试卷列表"
    api="/examlib/get_exam_list"
    :params="filterVal"
    :columnConfig="columnConfig"
    :reload="reload"
    :userId="true"
  >
    <template v-slot:ope>
      <div class="el_ope">
        <el-input
          placeholder="请输入试卷名称或试卷ID"
          v-model="filterVal.key_word"
          class="input-with-select"
          style="width: 320px;margin-right: 16px"
        >
          <el-button
            slot="append"
            class="teacher_search_bt"
            icon="el-icon-search"
            @click="reload = !reload"
          ></el-button>
        </el-input>
        <self-btn txt="上传试卷" @click="upload"></self-btn>
      </div>
    </template>
    <template v-slot:default="rowdata">
      <div v-if="rowdata && rowdata.rowdata">
        <div v-if="rowdata.rowdata.key == 'name'">
          <router-link
            :to="'/main/examlibrary/'+rowdata.rowdata.id+rowdata.rowdata.subject"
          >{{rowdata.rowdata.name}}</router-link>
        </div>
        <div v-if="rowdata.rowdata.key == 'ope'">
          <span
            v-show="rowdata.rowdata.can_edit"
            style="color:rgba(40,170,156,1);cursor: pointer"
            @click="edit(rowdata.rowdata)"
          >编辑</span>
          <span
            v-show="rowdata.rowdata.can_remove"
            style="color: rgba(255,102,102,1);margin-left: 15px;display: inline-block;cursor: pointer"
            @click="deleteExamHandler(rowdata.rowdata)"
          >删除</span>
        </div>
      </div>
    </template>
  </self-table>
</template>

<script>
export default {
  data() {
    return {
      filterVal: {
        type: 1,
        grades: [], // 年级
        subject: "", // 学科
        textbooks: [], // 教材
        test_types: [0, 1], // 类型
        key_word: ""
      },
      columnConfig: [
        { label: "试卷编号", key: "id" },
        { label: "试卷名称", key: "name", slot: true },
        {
          label: "年级",
          key: "gread",
          adapter(v) {
            return filtersGrade(v);
          }
        },
        {
          label: "科目",
          key: "subject",
          adapter(v) {
            return examSub(String(v));
          }
        }
      ],
      reload: false,
    };
  }
};
</script>

