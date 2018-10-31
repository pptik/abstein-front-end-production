<template>
  <div class="ui grid stackable">
    <div class="eight wide column grey-text">
      <h2 class="primary-color">Form Registrasi Pengguna</h2><br/>
      <form class="ui form">
        <div class="field">
          <div>Nama Sekolah</div>
          <input type="text" name="nama_sekolah" placeholder="Buat username Anda" v-model="nama_sekolah"/>
        </div>
        <div class="field">
          <div>Username</div>
          <input type="text" name="username" placeholder="Sandi Anda untuk login" v-model="username"/>
        </div>                
        <div class="field">
          <div>Email</div>
          <input type="email" name="email" placeholder="Tulis Nama Lengkap Anda" v-model="email"/>
        </div>
         <div class="field">
          <div>Password</div>
          <input type="password" name="password" placeholder="Tulis Nama Lengkap Anda" v-model="password"/>
        </div>
        <button v-on:click.prevent="daftarSebagaiMahasiswa" style="color: white; background: linear-gradient(141deg, #2ecc71 10%, #27ae60 51%, #27ae60 75%);" class="ui button">Kirim</button>
      </form>
    </div>
   <div  class="eight wide column" align="center">      
      <div class="ui grid stackable">
        <div class="sixteen wide column" style="margin-top:2em;">
          <img src="/src/assets/images/Tutwurihandayani.png" style="heigh:350; width:350;" class="ui image medium"/>          
        </div>        
      </div>        
    </div>
  </div>

</template>

<script>
  import global_json from '../../assets/js/globalVariable.json';
 import Swal from 'sweetalert2'
  export default {
    name: "konten",
    data(){
      return{
        nama_sekolah: '',
        username: '',
        email: ''
      }
    },
    created(){
      this.daftarUniversitas()
    },
    methods: {
      daftarSebagaiMahasiswa:function () {
        Swal({
    //title: 'Processing...',
    html: 'Harap tunggu Data Sedang di proses...',
    allowOutsideClick : false,
    onOpen: () => {
      Swal.showLoading()
    },
  })
        this.$http.post(global_json.general_url+'/absen/sekolah/create',{
          nama_sekolah: this.nama_sekolah,
          username: this.username,
          email: this.email,
          password: this.password
        }).then(function (data) {
          //console.log(data);
          if(data.body.success == true){
           Swal({title:data.body.data.message});
          }else if(data.body.success == false){
            var pesan = '';
            //console.log('Kembalian: '+data.body.data.length)
            for(let a=0;a<data.body.data.length;a++){
              pesan += JSON.stringify(data.body.data[a].msg)+', ';
            }
            Swal("Terjadi Kesalahan,SIlahkan Hub Admin");
          }
        });
      },
      daftarUniversitas: function(){
        this.$http.post(global_json.general_url+ '/institusi/daftar/no_token', {          
        }).then(function (data) {
          if (data.body.success == true) {            
            data.body.data.data.forEach(element => {
              this.daftar_universitas.push(element)
            });            
          } else if (data.body.success == false) {
            console.error('M: Gagal mengembalikan daftar universitas yang terdaftar dalam platform TADJ')
          }
        });
      },
      daftarJenjang: function(){
        this.$http.post(global_json.general_url + '/jenjang/daftar/no_token', {          
          universitas: this.select_universitas
        }).then(function (data) {
          if (data.body.success == true) {
            this.daftar_jenjang.length = 0            
            for(let counter=0;counter<data.body.data.data[0].akademik.jenjang.length;counter++){
               console.log("JNJNG ke["+counter+"]: "+JSON.stringify(data.body.data.data[0].akademik.jenjang[counter].nama))
               this.daftar_jenjang.push(data.body.data.data[0].akademik.jenjang[counter]) 
            }

          } else if (data.body.success == false) {
            console.log('M: Gagal mengembalikan daftar jenjang pada universitas dalam platform TADJ')
          }
        });
      },
      daftarProdi: function(){
        this.$http.post(global_json.general_url + '/jenjang/prodi/no_token', {          
          jenjang: this.select_jenjang
        }).then(function (data) {
          if (data.body.success == true) {
            this.daftar_prodi.length = 0            
            for(let counter=0;counter<data.body.data.data[0].prodi.length;counter++){
               this.daftar_prodi.push(data.body.data.data[0].prodi[counter]) 
            }                      
          } else if (data.body.success == false) {
            console.log('M: Gagal mengembalikan daftar prodi pada platform TADJ')
          }
        });
      }
    }
  }




</script>

<style scoped>

</style>
