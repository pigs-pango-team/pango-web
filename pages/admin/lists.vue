<template>
  <v-container fluid pa-0 class="font">
    <div class="contents">
      <div style="font-size: 32px; font-weight: bold; margin-bottom: 48px;">
        광고 현황
      </div>

      <div class="search">
        <v-row no-gutters align="center">
          <v-col md="2">
            <v-menu
              ref="isSelectedDate"
              v-model="isSelectedDate"
              :close-on-content-click="false"
              :return-value.sync="date"
              transition="scale-transition"
              offset-y
              min-width="290px"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-text-field
                  v-model="date"
                  prepend-icon="mdi-calendar"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                ></v-text-field>
              </template>
              <v-date-picker v-model="date" no-title scrollable>
                <v-spacer></v-spacer>
                <v-btn text color="primary" @click="isSelectedDate = false">
                  Cancel
                </v-btn>
                <v-btn text color="primary" @click="$refs.isSelectedDate.save(date)">
                  OK
                </v-btn>
              </v-date-picker>
            </v-menu>
          </v-col>
          <v-col md="1" offset="1">
            <v-select :items="pages" label="page"></v-select>
          </v-col>
          <v-col md="1" offset="1">
            <v-select v-model="select" :items="titles" single-line></v-select>
          </v-col>
          &nbsp;
          <v-col md="2">
            <v-text-field label="검색어" clearable></v-text-field>
          </v-col>
          <span>
            <button type="button">
              <v-img :src="searchIcon" style="width: 30px; heigth: 30px;"></v-img>
            </button>
          </span>
        </v-row>
      </div>

      <div class="sort">
        <v-row no-gutters style="height: 84px;" align="center">
          <v-col>
            <input v-model="isCheckAll" type="checkbox" @click="checkAll" />
          </v-col>
          <v-col md="4" align="center">
            광고 제목
          </v-col>
          <v-col md="1" align="center">시작 날짜</v-col>
          <v-col md="1" align="center">종료 날짜</v-col>
          <v-col align="center">기간</v-col>
          <v-col md="3"></v-col>
        </v-row>
        <ul>
          <li v-for="(poster, idx) in posters" :key="idx">
            <v-row no-gutters style="height: 84px;" align="center">
              <v-col>
                <input v-model="checked" :value="poster" type="checkbox" @change="updateCheckall" />
              </v-col>
              <v-col md="4" align="center">{{ poster.title }}</v-col>
              <v-col md="1" align="center">{{ poster.startedDate }}</v-col>
              <v-col md="1" align="center">{{ poster.finishedDate }}</v-col>
              <v-col align="center">개월</v-col>
              <v-col md="1" align="center">
                <input type="button" value="상세정보" style="font-weight: bold;" />
              </v-col>
              <v-col md="1" align="end">
                <input class="statusBtn" type="button" value="정보 수정" />
              </v-col>
              <v-col md="1" align="center">
                <input class="statusBtn" type="button" value="게시 중단" />
              </v-col>
            </v-row>
          </li>
        </ul>
      </div>
    </div>
  </v-container>
</template>

<script>
import axios from 'axios';
export default {
  layout: 'admin/default',
  data() {
    return {
      date: new Date().toISOString().substr(0, 10),
      isSelectedDate: false,
      pages: ['1', '2', '3', '4', '5'],
      select: '제목',
      titles: ['제목', '시작 날짜', '기간'],
      posters: [],
      isCheckAll: false,
      checked: [],
      searchIcon: require('~/static/icon/search.svg'),
    };
  },
  created() {
    const vm = this;
    axios
      .get('https://gg-pigs.com:8484/api/v1/advertisements')
      .then(function(response) {
        vm.posters = response.data.data;
      })
      .catch(function(error) {
        console.log(error);
      });
  },
  methods: {
    checkAll() {
      this.isCheckAll = !this.isCheckAll;
      this.checked = [];
      if (this.isCheckAll) {
        for (const key in this.posters) {
          this.checked.push(this.posters[key]);
        }
      }
    },
    updateCheckall() {
      if (this.checked.length === this.posters.length) {
        this.isCheckAll = true;
      } else {
        this.isCheckAll = false;
      }
    },
  },
};
</script>

<style scoped>
a {
  color: black;
  text-decoration: none;
}
ul {
  list-style-type: none;
  padding-left: 0;
}
li {
  height: 84px;
  border-radius: 12px;
  border: solid 1px #707070;
  margin-bottom: 20px;
}
input[type='checkbox'] {
  transform: scale(2);
  margin: 0 40px;
}
.container {
  display: flex;
  justify-content: center;
}
.contents {
  width: 100%;
  padding: 66px 5%;
}
.search {
  height: 84px;
  border-radius: 12px;
  background-color: #f0f0f0;
  display: flex;
  align-items: center;
  padding: 0 25px;
}
.font {
  font-family: 'Noto Sans KR', sans-serif;
}
.statusBtn {
  width: 100px;
  height: 45px;
  border-radius: 11px;
  background-color: #f0f2f8;
}
</style>
