 
<template>
  <div class="background">

    <!-- <button @click="dsa">asdas</button> -->

     
    <div class="flex-left">

     <svg   xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
  <path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
</svg><input  class="searh" type="text" v-model="searchString" @input="pushQueryToUrl({search: searchString})" />

     </div>
     

 <div class="header"> 
    <h1 class="page">Products</h1>
     

    <div class="category">
    <div v-for="c in categories.data" :key="c.id">
       
 
       <button class="gradient-button" @click="pushQueryToUrl({categoryId: c.id})"><div>{{ c.title }}</div></button> 
       
       </div>
       </div>

    </div>
    

    <div class="picture"></div>

    <div>

    <select class="sorting" name="sort" v-model="sort" @change="asd($event)">
      <option value="updatedAt:desc">Newest</option>
      <option value="price:asc">Price: Ascending</option>
      <option value="price:desc">Price: Descending</option>
    </select>

    </div>

     
     
     
    <div class="flex-product">
      
    <div class="size" v-for="p in products.data" :key="p.id">
      <p class="title">{{ p.title }}</p>
      <img src="https://chocomart.kz/upload/38/38197dee468f9a74c4a6ef497230b07a.jpg">
      <p class="price">{{ p.price }} <span class="dollar">$</span> </p>
      <!-- <p>{{ p.spec }}</p> -->
       
    </div>
    </div>


    <div v-for="i in products?.meta?.pagination?.pageCount" :key="i">
      <button @click="pushQueryToUrl({page: i})">{{ i }}</button>
    </div>
    <div>
       <input type="checkbox"   value='wifi:5' @change="pushQueryToUrl({spec: $event.target.value})">wifi 5
       <input type="checkbox"   value='let:true' @change="pushQueryToUrl({spec: $event.target.value})">true
       <input type="checkbox"   value='Lidar:true' @change="pushQueryToUrl({spec: $event.target.value})">lidar
       
    </div>
  </div>
   

</template>
<script>
 

import { useECommerceStore } from "@/stores/e-commerce";
import { useRoute, useRouter } from "vue-router";
import { ref, onMounted, watchEffect } from "vue";
import { storeToRefs } from 'pinia'
import qs from 'qs'
export default {
  setup() {
    const eCommerceStore = useECommerceStore();
    const { products } = storeToRefs(eCommerceStore);
    const { categories } = storeToRefs(eCommerceStore);
    const route = useRoute();
    const router = useRouter();
    const sort = ref(route.query.sort || "updatedAt:desc");
    const searchString = ref('')
    const spec = ref()
    let ezQuery = {};
    function asd(event) {
      pushQueryToUrl({ sort: event.target.value });
    }
    function pushQueryToUrl(queryParam) {
      Object.entries(queryParam).forEach(([key, value]) => {
        if (value) {
          ezQuery[key] = value;

        }else{
          ezQuery[key] = undefined;
        }
      });
      
      router.push({query: ezQuery})
    }
    async function createProductQuery() {
      ezQuery = {
        page: route.query.page,
        gte: route.query.gte,
        lte: route.query.lte,
        sort: route.query.sort,
        categoryId: route.query.categoryId,
        spec: route.query.spec ? route.query.spec.split(',').map(s => s.split(':')) : undefined,
        search: route.query.search != '' || route.query.search ? route.query.search : undefined
      }
      console.log(ezQuery.spec)
      const pagination = {page: route.query.page || 1, pageSize: 5};
      const populate = ["category"];
      const sort = route.query.sort ? [route.query.sort] : ["updatedAt:desc"];
      const search = route.query.search != '' && route.query.search ? route.query.search : undefined
      const spec = route.query.spec
      const filters = {
          $and: [
            {
              price: {
                $gte: route.query.price_gte,
              },
            },
            {
              price: {
                $lte: route.query.price_lte,
              },
            },
            {
              category:{
                id: {
                  $eq: route.query.categoryId || categories[0]?.id,
                }
              }
            },
            {
              title: {
                $startsWith: search,
              }
            },
          ],
        };
      if (ezQuery.spec) {
        ezQuery.spec.map(s => {
          filters.$and.push({
            spec: {
              $containsi: `"${s[0]}":${s[1]}`
            }
          })
        })
      }
     
      
      const query = {
        populate,
        pagination,
        sort,
        filters,
      }
      await eCommerceStore.fetchCategories();
      await eCommerceStore.fetchProducts(query);
      console.log(filters);
    }
    watchEffect(() => {      
      createProductQuery();
    });
    onMounted(() => {});    
    return {
      products,
      eCommerceStore,
      pushQueryToUrl,
      categories,
      sort,
      asd,
      searchString
    };
  },
  name: "Products",
};
</script>
 

<style scoped>

.gradient-button {
  font-size: 10px ;
  text-decoration: none;
  color: white;
  padding: 10px 10px;
  margin: 0px 10px;
  border-radius: 10px;
  font-family: 'Montserrat', sans-serif;
  text-transform: uppercase;
  letter-spacing: 2px;
  background-image: linear-gradient(to right, rgb(95, 13, 31) 0%, rgba(163, 67, 12, 0.781) 51%, rgb(165, 44, 14) 100%);
  background-size: 200% auto;
  box-shadow: 0 0 20px rgba(0, 0, 0, .1);
  transition: .5s;
  
}
.gradient-button:hover {
  background-position: right center;
}
 .flex-left{display: flex; justify-content: flex-end}
 svg{height: 25px;}
 
.sorting{padding: 5px 10px;
border-radius: 10px;
background-color: rgb(199, 194, 194);
}
.page{margin-left: 100px;}
  
  .flex-product{display: flex; justify-content: center}
  .size{  margin:0px 20px;   border-radius: 10px; transition: .8s; background-color: white; border: solid black 1px; }
  .size:hover{ box-shadow: 20px 10px 20px rgb(68, 30, 30);}
  img{height: 200px;}

  .header{background-image: url(https://www.ixbt.com/img/x780/n1/news/2021/9/0/FBlmFlnX0AIbDre_large.jpg); height:400px;background-position-x:0px;   animation:gorod 100s infinite linear; background-position:bottom;}

@keyframes gorod{
0%{background-position-x:0%;}
45%{background-position-x:90%;}
50%{background-position-x:100%;}
}
  
  .category{display: flex;    }

  .header,h1{color:azure}

    

  .title{font-size: 20px; font-family:-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif; font-weight: 400 ;}

  .dollar{color: brown; font-size: 20px}

  .price{font-size: 20px;}
  .picture{background-image: url(https://im6.bloha.ru/2021/08/maxresdefault-10.jpg?x75910); background-position: center;  background-repeat: no-repeat; background-color: beige; background-size: 1000px;
  height: 500px;  }
</style>
 