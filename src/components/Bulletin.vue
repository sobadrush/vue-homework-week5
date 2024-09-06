<template>
  <div>
    <h1 style="color: blue;">Bulletin.vue</h1>

    <fieldset class="border border-dark p-3">
      <legend class="w-auto px-2">查詢區塊</legend>
      <div class="row">
        <div class="col-4">
          <label for="bTitle" class="d-inline">公告標題：</label>
          <input type="text" class="form-control d-inline w-auto" id="bTitle" v-model="q_title"/>
          
          <br/><br/>
          
          <label class="d-inline">公告種類：</label>
          <select class="form-control d-inline w-auto" v-model="q_type">
            <option value=""></option>
            <option value="1">最新公告</option>
            <option value="2">FAQ</option>
            <option value="3">操作手冊</option>
          </select>

          <br/><br/>

          <label class="d-inline">是否公開：</label>
          <select class="form-control d-inline w-auto" v-model="q_published">
            <option value=""></option>
            <option value="true">公開</option>
            <option value="false">非公開</option>
          </select>

        </div>
        <div class="col-5">
          
          <!-- <div class="row mb-3">
            <label for="start-time" class="col-sm-2 col-form-label">開始時間：</label>
            <div class="col-sm-4">
              <input type="datetime-local" class="form-control" id="start-time">
            </div>
          </div> -->
          <div class="row mb-3">
            <label for="end-time" class="col-sm-2 col-form-label">基準日：</label>
            <div class="col-sm-4">
              <input type="date" class="form-control" id="end-time" v-model="q_endTime">
            </div>
          </div>
          <p>(查詢 endTime 在「基準日」以後的資料)</p>
          <button type="button" class="btn btn-outline-info" @click.prevent="doQueryBulletinMsgs">查詢</button>
        </div>
        <div class="col">
          <!-- section3 -->
        </div>
      </div>
    </fieldset>

    <br/>

    <fieldset class="border border-dark p-3">
      <legend class="w-auto px-2">新增區塊</legend>

      <label class="d-inline">公告標題：</label>
      <input type="text" class="form-control d-inline w-auto" v-model="p_title" />
      
      <br/><br/>

      <label class="d-inline">是否公開：</label>
      <input type="text" class="form-control d-inline w-auto" v-model="p_published" />

      <br/><br/>

      <label class="d-inline">公告種類：</label>
      <select class="form-control d-inline w-auto" v-model="p_type">
        <option value="1">最新公告</option>
        <option value="2">FAQ</option>
        <option value="3">操作手冊</option>
      </select>

      <br/><br/>
      <button type="button" class="btn btn-outline-success" @click="(e) => doAddBulletin(e)">新增</button>
    </fieldset>
      
    <br/>
    
    <table class="table table-success table-striped table-hover table-bordered">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">id</th>
          <th scope="col">title</th>
          <th scope="col">type</th>
          <th scope="col">published</th>
          <th scope="col">startTime</th>
          <th scope="col">endTime</th>
          <th scope="col">followerCount</th>
          <th scope="col">action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in queryResults" :key="item.id">
          <th scope="row">{{ index + 1 }}</th>
          <td>{{ item.id }}</td>
          <td>{{ item.title }}</td>
          <td>{{ mapBulletinType(item.type) }}</td>
          <td>{{ item.published ? '公開':'非公開' }}</td>
          <td>{{ item.startTime }}</td>
          <td>{{ item.endTime }}</td>
          <td>{{ item.followerCount }}</td>
          <td style="text-align: center;">
            <button type="button" class="btn btn-danger" @click.prevent="deleteBulletin(item.id)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- <pre>{{ bulletinMsgs }}</pre> -->
  </div>
</template>

<script setup>
  import { ref, watchEffect } from 'vue';
  import { v4 as uuidv4 } from 'uuid';

  const bulletinMsgs = ref([
    {
      id: 'b655d5fc',
      title: 'Vue3 精選',
      type: 1, // 1: 最新公告, 2: FAQ, 3: 操作手冊
      published: true,
      startTime: '2024-10-01 00:00:00',
      endTime: '2024-10-31 23:59:59',
      followerCount: 87
    }, 
    {
      id: '9df91fac',
      title: '馬克與瑪麗',
      type: 2,
      published: true,
      startTime: '2022-07-07 13:58:00',
      endTime: '2024-02-12 15:45:23',
      followerCount: 1943
    }, 
    {
      id: '7382747e',
      title: 'SpringBoot 聖經',
      type: 3, // 1: 最新公告, 2: FAQ, 3: 操作手冊
      published: false,
      startTime: '2024-05-01 00:00:00',
      endTime: '2024-07-31 23:59:59',
      followerCount: 103
    }
  ])

  // 查詢結果容器
  const queryResults = ref([]);
  queryResults.value = bulletinMsgs.value;

  const p_title = ref('Angular全家桶');
  const p_published = ref('非公開');
  const p_type = ref('1');

  // 查詢相關參數
  const q_title = ref('');
  const q_type = ref('');
  const q_published = ref('');
  const q_endTime = ref('');

  /**
   * 新增公告
   */
  const doAddBulletin = (_event) => {
    console.log('doAddBulletin _event', _event);
    bulletinMsgs.value.push({
      id: genBulletinId(),
      title: p_title.value,
      type: parseInt(p_type.value),
      published: p_published.value === '公開' ? true : false,
      startTime: new Date().toISOString().slice(0, 19).replace('T', ' '),
      endTime: new Date(Date.now() + 30 * 24 * 60 * 60 * 1000).toISOString().slice(0, 19).replace('T', ' '),
      followerCount: 0
    })
  }


  /**
   * 產生公告id
   */
  const genBulletinId = () => {
    return uuidv4().split('-')[0];
  }


  /**
   * 刪除公告
   */
  const deleteBulletin = (_id) => {
    console.log(`刪除 id: ${_id}`)
    let targetIndex = bulletinMsgs.value.map(e => e.id).indexOf(_id)
    console.log('>>> targetIndex = ', targetIndex);
    
    console.log(">>> targer item to delete: ", bulletinMsgs.value[targetIndex])
    let delItem = bulletinMsgs.value.splice(targetIndex, 1)
    console.log(">>> delItem = ", delItem);
  }


  /**
   * 查詢公告
   */
  const doQueryBulletinMsgs = () => {
    console.log('>> q_title:', q_title.value);
    console.log('>> q_type: ', q_type.value);
    console.log('>> q_published: ', q_published.value);
    console.log('>> q_endTime: ', q_endTime.value);

    if (q_title.value === '' && q_type.value === '' && q_published.value === '' && q_endTime.value === '') {
      queryResults.value = bulletinMsgs.value;
      return;
    }

    // 查詢邏輯(and)
    // filter: https://www.casper.tw/javascript/2017/06/29/es6-native-array/#Array-prototype-filter
    queryResults.value = bulletinMsgs.value.filter(item => {
      let isMatch = true;
      if (q_title.value !== '') {
        isMatch = isMatch && item.title.includes(q_title.value);
      }
      if (q_type.value !== '') {
        isMatch = isMatch && item.type === parseInt(q_type.value);
      }
      if (q_published.value !== '') {
        isMatch = isMatch && item.published === (q_published.value === 'true');
      }
      if (q_endTime.value !== '') {
        // 判斷 item.endTime 是否在 q_endTime 之後
        isMatch = isMatch && new Date(item.endTime) > new Date(q_endTime.value);
      }
      return isMatch;
    });
  }


  /**
   * mapping 公告類別
   */
  const mapBulletinType = (_typeN) => {
    switch (_typeN) {
      case 1:
        return '最新公告';
      case 2:
        return 'FAQ';
      case 3:
        return '操作手冊';
  }}

  // watchEffect(() => {
  //   console.log('p_title: ', p_title.value);
  //   console.log('p_published: ', p_published.value);
  // });
</script>

<style lang="css" scoped>
  input {
    border: 1px solid #1a1313;
  }
  select {
    border: 1px solid #1a1313;
  }
</style>