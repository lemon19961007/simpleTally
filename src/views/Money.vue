<template>
  <Layout>
    <Tabs :data-source="typeList"
          @update:value="onUpdateType"
          :types="record.type"/>
    <Tags :tag-source.sync="tagList"
          @update:value="onUpdateTags"></Tags>
    <div class="notesWrapper">
      <MyInput file-name="备注"
               placeholder="请输入备注"
               class="notes"
               @update:value="onUpdateNotes"
               :value="record.notes"/>
    </div>
    <div class="createAt">
      <MyInput class="createAtInput"
               :value="record.createdAt"
               @update:value="getCreatedAt"
               type="date"/>
    </div>
    <NumberPad :amount="record.amount"
               @update:value="onUpdateAmount"
               @submit="saveRecord"></NumberPad>

  </Layout>
</template>


<script lang="ts">
import Layout from '@/components/Layout.vue';
import Tags from '@/components/Money/Tags.vue';
import NumberPad from '@/components/Money/NumberPad.vue';
import {Component} from 'vue-property-decorator';
import Tabs from '@/components/Tabs.vue';
import Vue from 'vue';
import Public from '@/public';
import createId from '@/lib/createId';
import store from '@/store';
import typeList from '@/constants/typeList';
import MyInput from '@/components/MyInput.vue';
import router from '@/router';


@Component({
  components: {MyInput, Layout, Tags, NumberPad, Tabs},
})
export default class Money extends Vue {
  // 对象初始化
  record: RecordItem = {
    id: '',
    tag: [],
    notes: '',
    type: '-',
    amount: 0,
    createdAt: new Date().toISOString()
  };

  typeList = typeList;

  get recordList() {
    return this.$store.state.recordList;
  }

  get tagList() {
    return this.$store.state.tagList;
  }

  created() {
    this.$store.commit('fetchRecords');
    this.$store.commit('fetchTags');
  }

  // 储存用户输入的record信息
  saveRecord() {
    if (!this.record.tag || this.record.tag.length === 0) {
      window.alert('请选择标签');
      return;
    }
    this.$store.commit('createRecord', this.record);
    if (this.$store.state.createRecordError === null) {
      window.alert('添加成功');
      this.record.notes = '';
    }
  }

  // 获取用户选择的标签
  onUpdateTags(value: { id: string, name: string, icon: string }[]) {
    this.record.tag = value;
  }

  // 获取用户输入备注信息
  onUpdateNotes(value: string) {
    this.record.notes = value;
  }

  // 获取支出收入选项信息
  onUpdateType(value: string) {
    this.record.type = value;     // '+'为收入，'-'为支出
  }

  // 获取计算器数据
  onUpdateAmount(value: string) {
    this.record.amount = parseFloat(value);
  }

  // 获取用户传输的创建时间
  getCreatedAt(value: any) {
    this.record.createdAt = value;
  }
};
</script>

<style lang="scss" scoped>
.notesWrapper {
  background-color: #f5f5f5;

  .notes {
    width: 240px;
    font-size: 13px;
    padding-left: 10px;
  }
}


.createAt {
  background-color: #f5f5f5;
  font-size: 13px;
  position: relative;

  .createAtInput {
    position: absolute;
    top: -7vh;
    left: 59%;
    line-height: 7vh;
  }
}

</style>