<template>
  <div class="hello">
    <div class="container">
      <div class="row">
        <div class="col pad_down">
          <h1>Post management</h1>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-md-4 .offset-md-4">New title:</div>
      <div class="col-md-4">New content:</div>
    </div>
    
    <div class="row pad_down">
      <div class="col-md-4">
        <input type="text" v-model="new_title" class="form-control ">
      </div>
      <div class="col-md-8 d-flex">
        <input type="text" v-model="new_content" class="form-control form_2">
        <button @click="add_post()" class="btn btn-primary">  Dodaj wpis</button>
      </div>
    </div>

  <h1>Post list:</h1>
  <ul class="list-group list-group-flush">
    <li v-for="(post, index) in posts" :key="post.id" class="list-group-item">
      <div class="row">
      <div class="col-md-8">{{post.title}} - {{post.content}}</div>
      <div class="col-md-4">
      <button class="btn btn-primary" :post_id="post.post_id" @click="delete_post($event,index)">Usuń post</button>
      </div>
    </div>
    </li>
  </ul>

  <div class="row">
    <div class="col-md-12">
      <h1>
        Filtered posts
      </h1>
    </div>
  </div>
  <div class="row">
    <div class="col-md-6">
      <input type="text" class="form-control" v-model="count_posts" placeholder="Wpisz liczbe postów">
    </div>
    <div class="col-md-6">
      <input type="text" class="form-control" v-model="date_posts" placeholder="Od kiedy">
    </div>
    <div class="row">
      <div class="col-md-12">
        <button class="btn btn-primary filer">Filter</button>
      </div>
    </div>


  </div>



  </div>
</template>

<script>
import db from './firebaseInit'  
export default {
  name: 'HelloWorld',
  data () {
    return {
      postRef : '',
      userRef : '',
      date_posts : '',
      new_title : '',
      new_content : '',
      count_posts : '',
      posts : [],
    }
  },
  beforeCreate(){
    console.log('before Created');
  },
  created (){
    console.log('created');
    db.collection('posts').get().then( (querySnapshot) => {
      querySnapshot.forEach( (doc) => {
        console.log(doc.data());

        const data = {
          'post_id' : doc.data().post_id,
          'title' : doc.data().title,
          'content' : doc.data().content
        }
        this.posts.push(data)

      })
    })
  },
  beforeMount (){
    console.log('before mounted');
  },
  mounted (){
    console.log('mounted');
  },
  methods : {
    show_posts(){
      this.count_posts = Number(this.count_posts)
      console.log(this.count_posts)
      this.postRef.where('date','>',this.date_posts).limit(this.count_posts).get()
      .then
    },

    delete_post(event,index){
      console.log(index);
      var post_index = index;
      var post_id = event.target.attributes.post_id.value;
      
      db.collection('posts').get().then( (querySnapshot) => {
        querySnapshot.forEach( (doc) => {

          if (doc.id == post_id) {
            doc.ref.delete();
          
            this.$delete(this.posts,post_index);
           }

        })
      })


    },






    add_post(){
      db.collection('posts').add({
        title : this.new_title,
        content : this.new_content
      }).then( docRef => {

        db.collection('posts').doc(docRef.id).set({
          post_id : docRef.id,
          title : this.new_title,
          content : this.new_content
        })

        // console.log(docRef.id)
        const data = {
          post_id : docRef.id,
          title : this.new_title,
          content : this.new_content 
        }

        this.posts.push(data)
      }).catch( error => {
        console.log('Coś poszło nie tak z dodawaniem do posts',error)
      })




      // .then(docRef => {
      //   console.log('Udało Ci się dodać wpis -',docRef.id)

      //   // db.collection('posts').doc(docRef.id).set({
      //   //   post_id : docRef.id,
      //   //   title : this.new_title,
      //   //   content : this.new_content
      //   // })

      //   const data = {
      //     title : this.new_title,
      //     content : this.new_content
      //   } 
      //   this.posts.push(data);     
      // })
      // .catch(error => {
      //   console.log('Wystąpił błąd podczas dodawania wpisu', error)
      // })
    }



  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
ul{
  list-style: none;
}
.pad_down{
  padding-bottom: 20px;
  margin-right: 10%;
  margin-left: 10%;
}
.filer{
  margin:0 auto;
}
</style>
