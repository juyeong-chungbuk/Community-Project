<template>
  <div class="overflow-auto">
  <div>
    <label for="search">🔍</label>
    <input v-model="search" type="text" @keyup.enter="searchkeyword($event.target.value)" class="search-input" placeholder="검색" autocomplete="off">
    <button class="rerender" @click="rerender">새로고침</button>
  </div>
      <b-table
      id="my-table"
      thClass="table-head"
      thStyle=""
      tbody-tr-class="table-body-tr"
      :items="searchedData"
      :fields="fields"
      :per-page="perPage"
      :current-page="currentPage"
      :hover="true"
      small
    >
    <template v-slot:cell(title)="{ item }">
        <router-link :to="{name: 'PostDetail', params: { item: item }}" @click.native="viewCountIncre(item)"
        class="title-link">
          <b>{{item.title}}</b>
        </router-link>
      </template>
      <template v-slot:cell(content)="{ item }">
          <p v-html="item.content">{{item.content}}</p>
      </template>
    </b-table>
    <div class="mt-3 paging">
      <b-pagination
      v-model="currentPage"
      :total-rows="rows"
      :per-page="perPage"
      pills
    ></b-pagination>
    </div>
  </div>
</template>

<script>
let url = "http://127.0.0.1:8000/community/viewcnt_save/";
  export default {
    name: 'Pagination',
    data() {
      return {
        perPage: 10,
        currentPage: 1,
        search: "",
        searchedData: [...this.postlist],
        count: 0,
        fields: [
            {
              key: 'id',
              label: '#',
              thStyle: {margin: '10%'},
              tdClass: 'table-td'
            },
            {
              key: 'title',
              label: '제목',
              tdClass: 'table-td'
            },
            {
              key: 'content',
              label: '내용',
              tdClass: 'table-td'
            },
            {
              key: 'writer_fk_id',
              label: '글쓴이',
              tdClass: 'table-td'
            },
            {
              key: 'view_cnt',
              label: '조회수',
              tdClass: 'table-td'
            },
            {
              key: 'created_dt',
              label: '작성일',
              tdClass: 'table-td'
            },
        ]
      }
    },
    props:{
        postlist: Array,
        userlist: Array
    },
    methods:{
      // 조회수 함수
        viewCountIncre(item){
          this.$store.commit("stepchange",{n: 1, item:item});
          item.view_cnt += 1;
          axios.post(url,item)
          .then((res)=>{
            console.log(res);
          })
          .catch((error)=>{
            console.log("err", error.response)
          });
        },
        keyword(e){
          return e;
        },
        // 검색함수
        searchkeyword(e){
          let keyword = e;
          console.log(keyword)
          const posturl = "http://127.0.0.1:8000/community/searchKeyword/"+keyword;
           axios({
            method: "GET",
            url: posturl 
          })
          .then(response => {
            this.searchedData = response.data;
            console.log(this.searchedData) 
          })
          .catch(response => {
            console.log("Failed", response);
          });
        },
        rerender(){
          this.searchedData = this.postlist
        },
        displaycontent(item2){
          return CKEditor.instances.editor1.getData()
        }
    },
    computed: {
      // 전체 게시물 개수
      rows() {
        return this.postlist.length
      },
    },
    // 원본 복제
    updated(){
        if(this.count === 0){
          this.searchedData = [...this.postlist]
          this.count+=1
        }
        return this.searchedData
    }
  }
</script>
<style scoped>
.table-td{
  height: 300px;
}
.search-input:-ms-clear{
  display: none;
  border: 1px solid rgb(248, 231, 231);
}
.searchclear {
  position: absolute;
  right: 6px;
  top: 7px;
  bottom: 15px;
  width: 10px;
  height: 14px;
  margin: auto;
  font-size: 15px;
  cursor: pointer;
  color: #ccc;
  background-color: #fff;
}
.title-link{
  color: black;
  text-decoration: none;
}
.paging{
  text-align: center;
  margin: 0 auto;
}
.rerender{
  float: right;
}
</style>

    // <button @click="searchkeyword">검색하기</button>

              // this.searchedData = [...this.postlist]
          // this.search = e;

          // for(let i=0; i<this.searchedData.length; i++){
          //   if(!this.searchedData[i].title.includes(this.search)){
          //     this.searchedData.splice(i,1);
          //     i--;
          //   }
          // }
          // if(this.searchedData.length === this.postlist.length){
          //   this.searchedData = [...this.postlist]
          // }