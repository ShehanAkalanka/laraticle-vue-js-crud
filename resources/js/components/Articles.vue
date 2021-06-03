<template>
    <div>
        <h2>this is Article list</h2>
        <form @submit.prevent="addArticle" class="mb-3">
            <div class="form-group">
                <input type="text" class="form-control" v-model="article.title" placeholder="Title">
            </div>
            <div class="form-group">
                <textarea class="form-control" v-model="article.body" placeholder="body"></textarea>
            </div>
            <button type="submit" class="btn btn-light btn-block">add</button>
        </form>
        <nav aria-label="Page navigation example">
        <ul class="pagination">
            <li v-bind:class="[{disabled: !pagination.prev_page_url}]" class="page-item"><a @click="fetchArticles(pagination.prev_page_url)" class="page-link" href="#">Previous</a></li>
            <li class="page-item disabled"><a class="page-link text-dark" href="#">page{{pagination.current_page}} of {{pagination.last_page}}</a></li>
            <li v-bind:class="[{disabled: !pagination.next_page_url}]" class="page-item"><a @click="fetchArticles(pagination.next_page_url)" class="page-link" href="#">Next</a></li>
        </ul>
        </nav>
        <div class="cart card-body" v-for="article in articles" v-bind:key="article.id">
            <h3>{{article.title}}</h3>
            <p>{{article.body}}</p>
            <button @click="editArticle(article)" class="btn btn-warning">Edit</button>
             <button @click="deleteArticle(article.id)" class="btn btn-danger">Delete</button>
        </div>
    </div>
</template>
<script>
export default {
   data(){
       return {
           articles:[],
           article:{
               id:'',
               title:'',
               body:''
           },
           article_id:'',
           pagination:{},
           edit:false
       };
   },
   created(){
       this.fetchArticles();
   },
   methods:{
       fetchArticles(page_url){
           let vm = this;
           page_url = page_url || 'api/articles'
           fetch(page_url)
           .then(res =>res.json())
           .then(res =>{
               this.articles=res.data;
               vm.makePagination(res.meta, res.links)
           })
           .catch(err=>console.log(err));
       },
       makePagination(meta, links){
           let pagination = {
               current_page:meta.current_page,
               last_page:meta.last_page,
               next_page_url:links.next,
               prev_page_url:links.prev
           };
           this.pagination = pagination;
       },
       deleteArticle(id){
           if(confirm('are you sure?')){
               fetch(`api/article/${id}`,{
                   method:'delete'
               })
               .then(res=>res.json())
               .then(data=>{
                   alert('artile removed');
                   this.fetchArticles();
               })
                .catch(err=>console.log(err));

           }
       },
       addArticle(){
           if(this.edit ===false){
               //add
               fetch('/api/article',{
                   method:'post',
                   body:JSON.stringify(this.article),
                   headers:{
                       'content-type':'application/json'
                   }
               })
               .then(res =>res.json())
               .then(data =>{
                   this.article.title='';
                   this.article.body='';
                   alert('article aded');
                   this.fetchArticles();
               })
               .catch(err=>console.log(err));
           }else{
               //update
               fetch('/api/article',{
                   method:'put',
                   body:JSON.stringify(this.article),
                   headers:{
                       'content-type':'application/json'
                   }
               })
               .then(res =>res.json())
               .then(data =>{
                   this.article.title='';
                   this.article.body='';
                   alert('article updated');
                   this.fetchArticles();
               })
               .catch(err=>console.log(err));
           }
       },
       editArticle(article){
           this.edit=true;
           this.article.id=article.id;
           this.article.article_id=article.id;
           this.article.title=article.title;
           this.article.body=article.body;
       }
   }
};
</script>
