<template>
  <div>
    <h1 style="color: blue;">Bulletin.vue</h1>
    
    <div style="border: 1px solid black; padding: 1rem;">
      <label>title: </label><input type="text" v-model="p_title" /><br/>
      <label>published: </label><input type="text" v-model="p_published" /><br/>
      <button type="button" class="btn btn-outline-success" @click="(e) => doAddBulletin(e)">新增</button>
    </div>

    <br/>
    
    <table class="table table-success table-striped table-hover table-bordered">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">id</th>
          <th scope="col">title</th>
          <th scope="col">published</th>
          <th scope="col">start_time</th>
          <th scope="col">end_time</th>
          <th scope="col">followerCount</th>
          <th scope="col">action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in bulletinMsgs" :key="item.id">
          <th scope="row">{{ index + 1 }}</th>
          <td>{{ item.id }}</td>
          <td>{{ item.title }}</td>
          <td>{{ item.published ? '公開':'非公開' }}</td>
          <td>{{ item.start_time }}</td>
          <td>{{ item.end_time }}</td>
          <td>{{ item.followerCount }}</td>
          <td style="text-align: center;">
            <button type="button" class="btn btn-danger">Delete</button>
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
      published: true,
      start_time: '2021-10-01 00:00:00',
      end_time: '2021-10-31 23:59:59',
      followerCount: 87
    }, 
    {
      id: '7382747e',
      title: 'SpringBoot 聖經',
      published: false,
      start_time: '2023-05-01 00:00:00',
      end_time: '2023-07-31 23:59:59',
      followerCount: 103
    }
  ])

  const p_title = ref('Angular全家桶');
  const p_published = ref('非公開');

  /**
   * 新增公告
   */
  const doAddBulletin = (_event) => {
    console.log('doAddBulletin _event', _event);
    bulletinMsgs.value.push({
      id: genBulletinId(),
      title: p_title.value,
      published: p_published.value === '公開' ? true : false,
      start_time: new Date().toISOString().slice(0, 19).replace('T', ' '),
      end_time: new Date(Date.now() + 30 * 24 * 60 * 60 * 1000).toISOString().slice(0, 19).replace('T', ' '),
      followerCount: 0
    })
  }

  /**
   * 產生公告id
   */
  const genBulletinId = () => {
    return uuidv4().split('-')[0];
  }

  // watchEffect(() => {
  //   console.log('p_title: ', p_title.value);
  //   console.log('p_published: ', p_published.value);
  // });
</script>

<style lang="css" scoped>

</style>