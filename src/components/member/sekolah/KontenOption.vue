<template>
  <span>    
    <div class="ui top attached tabular menu">
      <a class="active item" data-tab="first"><span class="grey-text">Setting Email</span></a>
      <a class="item" data-tab="second"><span class="grey-text">Setting Password</span></a>
    </div>
    <div class="ui bottom attached active tab segment grey-text" data-tab="first">     
      <div class="grey-text" style="font-size:xx-large;font-weight:300;margin-top:0.3em;">      
        Email Verification
      </div>
      <br/>
      <form class="ui form">
        <div class="field">
          <label class="grey-text">Email </label>
           <input type="email" autocomplete="email" placeholder="email" v-model="email"/>                
        </div>
        <div class="field">
          <label class="grey-text">Password </label>
           <input type="password" autocomplete="password" placeholder="email" v-model="password"/>                
        </div>
        <div class="field">
          <button type="button"
                  style="background: linear-gradient(141deg, #2ecc71 10%, #27ae60 51%, #27ae60 75%);color:#FFFFFF;"
                  v-on:click.prevent="UpdateEmail()" class="huge ui button button-submit">Update</button>          
          <!-- <router-link to="" class="white-text">Belum punya akun? Daftar disini</router-link> -->
        </div>
      </form>
      <br/>
      <br/>
    
      <br/>
    </div> 
     <div class="ui bottom attached tab segment grey-text" data-tab="second">     
      <div class="grey-text" style="font-size:xx-large;font-weight:300;margin-top:0.3em;">      
        Ganti Password
      </div>
      <br/>
      <form class="ui form">
        <div class="field">
          <label class="grey-text">Password Lama </label>
           <input type="password" autocomplete="password" placeholder="email" v-model="password_lama"/>                
        </div>
        <div class="field">
          <label class="grey-text">Password Baru </label>
           <input type="password" autocomplete="password" placeholder="email" v-model="password"/>                
        </div>
         <div class="field">
          <label class="grey-text">Password Baru verif</label>
           <input type="password" autocomplete="password" placeholder="email" v-model="password"/>                
        </div>
        <div class="field">
          <button type="button"
                  style="background: linear-gradient(141deg, #2ecc71 10%, #27ae60 51%, #27ae60 75%);color:#FFFFFF;"
                  v-on:click.prevent="UpdateEmail()" class="huge ui button button-submit">Update</button>          
          <!-- <router-link to="" class="white-text">Belum punya akun? Daftar disini</router-link> -->
        </div>
      </form>
      <br/>
      <br/>
    
      <br/>
    </div> 
  </span>
</template>

<script>
  import global_json from '../../../assets/js/globalVariable.json';
  import Chart from 'chart.js';
  import moment from 'moment/moment'
  import Swal from 'sweetalert2'
  import table_export from 'tableexport'
  //import 'jquery-ui/build/release.js'
  import datepicker from 'vuejs-datepicker'
  
  export default {
    name: "konten",
    created() {
      this.check_session();
      //this.get_absen("5b834f3c6e33f31321bfaabd","5b83585226ebef39747eac4b");
      //this.get_all_siswa();
      this.get_all_kelas();
      moment.locale('id')      
      this.this_full_date = moment(Date.now()).format('LL');
      this.select_date_get_absen = this.this_date
      this.select_month_get_absen = this.this_month
    },
    mounted: function () {
      $('.menu .item')
        .tab()
      ;
      $('select.dropdown').dropdown();
      this.june_graph()
      //this.get_absen_kelas_harian()
      $("#button-export-table").css('display','none')
      this.get_date_collections()
      this.getAllTahunAjaran()      
      this.getAllJurusan()      
      this.getAllTingkatKelas()      
    },
    methods: {
      createSiswa: function(){
        this.$http.post(global_json.general_url+'/daftar/proses/siswa_absensi',{
          email:this.create_siswa_email,
          username:this.create_siswa_username,
          nama_lengkap:this.create_siswa_nama_lengkap,
          jenis_kelamin:this.create_siswa_jenis_kelamin,
          sandi:this.create_siswa_sandi,
          sekolah:this.$session.get('id_sekolah'),
          rfid:this.create_siswa_kode_rfid,
          kelas:this.create_siswa_kelas,
        }).then(function (data,err) {
          console.log("pesan full: "+JSON.stringify(data.body))
          if(data.body.success == true){ 
            alert("Siswa berhasil ditambahkan")
          }else{
           alert(data.body.data[0].message)
          }        
        })
      },
      UpdateEmail: function(){
        console.log(this)
        if(this.password == null && this.email==null){
Swal({title:"Harap Mengisi Semua Input"});
        }else if(this.password == this.$session.get('password')){
           Swal({
    //title: 'Processing...',
    html: 'Harap tunggu Data Sedang di proses...',
    allowOutsideClick : false,
    onOpen: () => {
      Swal.showLoading()
    },
  })
        this.$http.post(global_json.general_url+'/absen/sekolah/update_email_verification',{
          pengguna:this.$session.get('user_id'),
          email_verification:this.email
        }).then(function (data,err) {
          if(data.body.success == true){ 
            Swal(data.body.data.message)
          }else{
           Swal(err)
          }        
        })
      }else{
Swal({title:"Password Yang Anda Masukan Salah"})
      }},
      getAllTingkatKelas: function(){
        this.$http.get(global_json.general_url+'/tingkat_kelas/',{}).then(function (data,err) {
          if(data.body.success == true){ 
            this.create_class_tingkat_lists = data.body.data
          }else{
            console.err(err)
          }        
        })
      },
      getAllTahunAjaran: function(){
        this.$http.get(global_json.general_url+'/tahun_ajaran/',{}).then(function (data,err) {
          if(data.body.success == true){ 
            this.create_class_tahun_ajaran_lists = data.body.data
          }else{
            console.err(err)
          }        
        })
      },
      getAllJurusan: function(){
        this.$http.get(global_json.general_url+'/jurusan/',{}).then(function (data,err) {
          if(data.body.success == true){ 
            this.create_class_jurusan_lists = data.body.data
          }else{
            console.err(err)
          }        
        })
      },
      downloadRekapitulasi: function(){
        let dateSplitter = this.datepickerrekapitulasi.toISOString().split("T")        
        let dateAdjusted = dateSplitter[0]+"T00:00:00.000+0000"
        this.daterekapitulasi = dateSplitter[0]
        let vue = this

        this.$http.post(global_json.general_url+'/absen/sekolah/harian',{
          sekolah_id:this.$session.get('id_sekolah'),
          //date_time:moment().get('year')+"-"+(moment().get('month')+1)+"-"+moment().get('date')+"T00:00:00.000+0000"
          date_time:dateAdjusted
        }).then(function (data) {
          if(data.body.success == true){   
              vue.sekolah_harian_rekapitulasi = []           
              var results = data.body.data; 
              //console.log("BISMILLAH: "+JSON.stringify(results))
              for(let counter=0;counter<results.length;counter++){
                if(results[counter].waktu_datang != 0 || results[counter].waktu_pulang != 0){
                  results[counter].waktu_datang = moment(results[counter].waktu_datang).format('HH.mm.ss')                  
                }
                if(results[counter].waktu_pulang != 0){                  
                  results[counter].waktu_pulang = moment(results[counter].waktu_pulang).format('HH.mm.ss')
                }
                
                vue.sekolah_harian_rekapitulasi.push(results[counter])
              }
              //$("#tabel-unduh-data-absensi").css("display","block") 
              //$("#tombol-unduh-data-absensi").css("display","block") TOMBOL UNDUH TABEL REKAPITULASI

              
          }else if(data.body.success == false){
            //var pesan = data.body.message;             
          }
        });


        //$("#button-unduh-rekapitulasi").css("display","block")
      },
      unggahDaftarSiswa: function(){
        console.log("unggah")
      },
      get_date_collections: function(){
        for(let month=1; month<13; month++){
          this.monthCollections.push(moment(month, 'MM').format('MMMM'))
        }
      },
      show_update_form_absent: function(namaLengkap,waktuDatang,waktuPulang,rfidParam){
        this.update_nama_siswa = namaLengkap
        this.update_waktu_datang = waktuDatang
        this.update_waktu_pulang = waktuPulang
        this.update_rfid = rfidParam
        
        this.$modal.show('form-update-absensi');
      },
      close_update_form_absent: function(){
        this.$modal.hide('form-update-absensi');
      },
      export_tabel: function(){
        this.this_class = $("#select-kelas option:selected").text();
        let vue = this
        table_export(document.getElementById("tabel-unduh-data-absensi-1"),{          
          formats: ['xlsx', 'csv'],            // (String[]), filetype(s) for the export, (default: ['xlsx', 'csv', 'txt'])
          filename: 'Absensi '+vue.this_date+' '+vue.this_month+' '+vue.this_year+' '+vue.this_class,                             // (id, String), filename for the downloaded file, (default: 'id')          
          exportButtons: true,                        // (Boolean), automatically generate the built-in export buttons for each of the specified formats (default: true)
          position: 'bottom',                         // (top, bottom), position of the caption element relative to table, (default: 'bottom')
          ignoreRows: null,                           // (Number, Number[]), row indices to exclude from the exported file(s) (default: null)
          ignoreCols: null,                           // (Number, Number[]), column indices to exclude from the exported file(s) (default: null)
          trimWhitespace: true                        // (Boolean), remove all leading/trailing newlines, spaces, and tabs from cell text in the exported file(s) (default: false)
        })
      },
       export_tabel_rekapitulasi: function(){
        //this.this_class = $("#select-kelas option:selected").text();
        let vue = this
        table_export(document.getElementById("tabel-unduh"),{          
          formats: ['xlsx', 'csv'],            // (String[]), filetype(s) for the export, (default: ['xlsx', 'csv', 'txt'])
          filename: 'Absensi '+vue.daterekapitulasi,                             // (id, String), filename for the downloaded file, (default: 'id')          
          exportButtons: true,                        // (Boolean), automatically generate the built-in export buttons for each of the specified formats (default: true)
          position: 'bottom',                         // (top, bottom), position of the caption element relative to table, (default: 'bottom')
          ignoreRows: null,                           // (Number, Number[]), row indices to exclude from the exported file(s) (default: null)
          ignoreCols: null,                           // (Number, Number[]), column indices to exclude from the exported file(s) (default: null)
          trimWhitespace: true                        // (Boolean), remove all leading/trailing newlines, spaces, and tabs from cell text in the exported file(s) (default: false)
        })
      },
      check_session(){
        if(this.$session.get('access_token') == null){
          alert("Session anda sudah habis, silahkan login kembali untuk melanjutkan.")
          this.$router.push('/')
        }
      },
      handleFileUpload: function(){
        this.image = this.$refs.file.files[0];
        this.submitFile()        
      },
      submitFile: function(){
        let thisParent = this
        let formData = new FormData();
        formData.append('sheet', this.image)
        
        axios.post(global_json.general_ftp_url+'/files/upload/image',
                formData,
                {
                headers: {
                    'Content-Type': 'multipart/form-data'
                }
              }
        ).then(function(data){
          //console.log("Kembalian ambil image path: "+JSON.stringify(data))
          thisParent.imagePath = data.data.results.http_path          
          $("#tombol-ganti-foto-profil").removeAttr("disabled");          
          $("#tombol-ganti-foto-profil").html('Kirim')
        })
        .catch(function(err){
          console.log('Upload ke ftp gagal: '+err);
        });        
      },
      get_absen(user){
        let vue = this

        Swal({
        allowOutsideClick: false,
        text: 'Mohon tunggu permintaan Anda sedang diproses...',        
        onOpen: function () {
          Swal.showLoading()
          console.log("Start time month: "+parseInt(moment().get('month')+1))
          console.log("End time month: "+parseInt(moment().get('month')+2))
          vue.$http.post(global_json.general_url+'/absen/query',{
            sekolah_id:vue.$session.get('id_sekolah'),
            start_time:moment().get('year')+"-"+parseInt(moment().get('month')+1)+"-01T00:00:00.000+0000",
            end_time:moment().get('year')+"-"+parseInt(moment().get('month')+2)+"-01T00:00:00.000+0000",
            /* start_time:"2018-07-01T00:00:00.000+0000",
            end_time:"2018-08-01T00:00:00.000+0000", */
            user_id:user
          }).then(function (data) {
            if(data.body.success == true){
              
              var results = data.body.data.resultArray;
              console.log("SSADS: "+results.length)
              if(results.length>0){
                //alert("data")
                vue.chartLabel=[];
                vue.waktuDatang=[];
                vue.waktuPulang=[];
                vue.tepatDatang=[];
                vue.tepatPulang=[];
                for(let counter=0;counter<results.length;counter++){
                  console.log("XXI:"+moment(results[counter].tanggal_string).format('DD'))
                  vue.chartLabel.push(moment(results[counter].tanggal_string).format('DD'))
                  vue.waktuDatang.push(vue.get_only_time(results[counter].datang_string).getTime()/1000)
                  vue.waktuPulang.push(vue.get_only_time(results[counter].pulang_string).getTime()/1000)
                  vue.tepatDatang.push(vue.get_only_time(results[counter].max_kedatangan_string).getTime()/1000);
                  vue.tepatPulang.push(vue.get_only_time(results[counter].max_kepulangan_string).getTime()/1000);
                }

                vue.june_graph();
                Swal.close()
              }else{
                Swal({title:'Maaf',
                text:'Data tidak tersedia.',
                type:'error',
                allowOutsideClick: false});                
              }

            
          }else if(data.body.success == false){
            var pesan = data.body.message;
            //console.log('Kembalian: '+data.body.data.length)
           
           Swal({title:'Maaf',
                text:pesan,
                type:'error',
                allowOutsideClick: false});                

            
          }
        });
        }})
                
      },
      get_absen_kelas_harian(){
        
        let vue = this

        Swal({
        allowOutsideClick: false,
        text: 'Mohon tunggu permintaan Anda sedang diproses...',        
        onOpen: function () {
          Swal.showLoading()

          let date,month
          
          vue.$http.post(global_json.general_url+'/absen/kelas/harian',{
            sekolah_id:vue.$session.get('id_sekolah'),
            kelas_id:vue.select_class_rekapitulasi,
            //date_time:vue.this_year+"-07-01T00:00:00.000+0000"
            date_time:new Date().toISOString() 
        }).then(function (data) {
          if(data.body.success == true){ 
              //console.log("Harian kelas: "+JSON.stringify(data.body.data))
              vue.sekolah_harian = []             
              var results = data.body.data; 
              for(let counter=0;counter<results.length;counter++){
                if(results[counter].waktu_datang != 0 || results[counter].waktu_pulang != 0){
                  results[counter].waktu_datang = moment(results[counter].waktu_datang).format('HH.mm.ss')                  
                }
                if(results[counter].waktu_pulang != 0){                  
                  results[counter].waktu_pulang = moment(results[counter].waktu_pulang).format('HH.mm.ss')
                }

                vue.sekolah_harian.push(results[counter])
                $("#button-export-table").css('display','block')
                Swal.close()
              }                         
          }else if(data.body.success == false){
            var pesan = data.body.message;
            //console.log('Kembalian: '+data.body.data.length)
           
             Swal({title:'Maaf',
                text:pesan,
                type:'error',
                allowOutsideClick: false}); 
          }
        });

        }})
        
      },
      get_all_siswa(){
        this.daftarSiswa = []
        this.$http.post(global_json.general_url+'/absen/siswa/filter/sekolah',{          
          sekolah: this.$session.get('id_sekolah')          
        }).then(function (data) {
          if(data.body.success == true){
              //alert(JSON.stringify(data.body))
              //console.log("daftar siswa dlm sekolah: "+JSON.stringify(data.body))
              var results = data.body.data.daftar_siswa_pada_sekolah;
              for(let counter=0;counter<results.length;counter++){
                this.daftarSiswa.push(results[counter])    
              }            
          }else if(data.body.success == false){
            var pesan = data.body.message;
            //console.log('Kembalian: '+data.body.data.length)
           
            //alert(pesan);
          }
        });
      },
      get_all_siswa_by_class(classe){
        this.daftarSiswa = []
        let vue = this

        Swal({
        allowOutsideClick: false,
        text: 'Mohon tunggu permintaan Anda sedang diproses...',        
        onOpen: function () {
          Swal.showLoading()
          vue.$http.post(global_json.general_url+'/absen/siswa/filter/kelas',{          
          kelas_id: classe
          }).then(function (data) {
            if(data.body.success == true){
                //alert(JSON.stringify(data.body))
                //console.log("daftar siswa dlm sekolah: "+JSON.stringify(data.body))
                var results = data.body.data;
                for(let counter=0;counter<results.length;counter++){
                  vue.daftarSiswa.push(results[counter])    
                }   
                Swal.close()         
            }else if(data.body.success == false){
              var pesan = data.body.message;
              //console.log('Kembalian: '+data.body.data.length)
              Swal.close()         
              alert(pesan);
            }
          });
        }})
                
      },
      get_all_siswa_by_class_rekapitulasi(classe){
        this.daftarSiswa = []
        this.$http.post(global_json.general_url+'/absen/siswa/filter/kelas',{          
          kelas_id: classe
        }).then(function (data) {
          if(data.body.success == true){
              //alert(JSON.stringify(data.body))
              //console.log("daftar siswa dlm sekolah: "+JSON.stringify(data.body))
              var results = data.body.data;
              for(let counter=0;counter<results.length;counter++){
                this.daftarSiswa.push(results[counter])    
              }            
          }else if(data.body.success == false){
            var pesan = data.body.message;
            //console.log('Kembalian: '+data.body.data.length)
           
            //alert(pesan);
          }
        });
      },
      get_all_kelas(){
        this.daftarSiswa = []
        this.$http.post(global_json.general_url+'/absen/kelas/filter/sekolah',{          
          sekolah: this.$session.get('id_sekolah')          
        }).then(function (data) {
          if(data.body.success == true){
              //alert(JSON.stringify(data.body))
              //console.log("daftar siswa dlm sekolah: "+JSON.stringify(data.body))
              var results = data.body.data.daftar_kelas_pada_sekolah;
              for(let counter=0;counter<results.length;counter++){
                this.daftarKelas.push(results[counter])    
                this.daftarKelasRekapitulasi.push(results[counter])
              }            
          }else if(data.body.success == false){
            var pesan = data.body.message;
            console.error(pesan)
           
            //alert(pesan);
          }
        });
      },
      get_all_sekolah(){
        this.$http.post(global_json.general_url+'/absen/sekolah/daftar',{
          /* sekolah_id:"5b834f3c6e33f31321bfaabd",
          start_time:"2018-07-01T00:00:00.000+0000",
          end_time:"2018-08-01T00:00:00.000+0000",
          user_id:"5b83585226ebef39747eac4b", */
        }).then(function (data) {
          if(data.body.success == true){
              //alert(JSON.stringify(data.body))
              var results = data.body.data.daftar_sekolah;
              for(let counter=0;counter<results.length;counter++){
                this.daftarSekolah.push(results[counter])    
              }            
          }else if(data.body.success == false){
            var pesan = data.body.message;
            //console.log('Kembalian: '+data.body.data.length)
           
            alert(pesan);
          }
        });
      },
      get_only_time(dateString){
        var date = new Date(dateString);
        return new Date(0,0,0,date.getHours(), date.getMinutes());
      },
      generated_data(){
        this.chartLabel=[];
        this.waktuDatang=[];
        this.waktuPulang=[];
        this.tepatDatang=[];
        this.tepatPulang=[];
        var minDatang = this.minDayEpoch-1000;
        var maxDatang = 1534812000;
        var minPulang = 1534831200;
        var maxPulang = this.maxDayEpoch+1000;
        for(let counter=0;counter<31;counter++){
          this.chartLabel.push(counter+1)
          this.waktuDatang.push(Math.floor(Math.random() *(maxDatang-minDatang+1)+minDatang))
          this.waktuPulang.push(Math.floor(Math.random() *(maxPulang-minPulang+1)+minPulang))
          this.tepatDatang.push(this.minDayEpoch);
          this.tepatPulang.push(this.maxDayEpoch);
        }

        this.june_graph();
      },
      june_graph: function(){
        var getParent = this
        var date =getParent.chartLabel 
        //var duration=[22101,22135,20047,20189,18752] 
        var duration=getParent.waktuDatang
        var duration2=getParent.waktuPulang
        var tepatDatang = getParent.tepatDatang
        var tepatPulang=getParent.tepatPulang

        var ctx = document.getElementById("myChart");
        var myChart = new Chart(ctx, {
          type: 'line',
          data: {
              labels: date,
              datasets: [
              {
                  label: 'Datang',
                  data: duration,
                  fill:false,
                  borderColor: [                      
                      'rgba(75, 192, 192, 1)',
                      'rgba(54, 162, 235, 1)',
                      'rgba(255, 206, 86, 1)',                      
                      'rgba(255,99,132,1)'
                  ],
                  borderWidth: 1
              },
              {
                  label: 'Pulang',
                  data: duration2,
                  fill:false,
                  borderColor: [                      
                      'rgba(75, 192, 192, 1)',
                      'rgba(54, 162, 235, 1)',
                      'rgba(255, 206, 86, 1)',                      
                      'rgba(255,99,132,1)'
                  ],
                  borderWidth: 1
              },{
                  label: "Batas",
                  data: tepatDatang,
                  fill:false,
                  borderColor: [                      
                      'rgba(255,99,132,1)',
                      'rgba(54, 162, 235, 1)',
                      'rgba(255, 206, 86, 1)',
                      'rgba(75, 192, 192, 1)',
                      'rgba(153, 102, 255, 1)'
                  ],
                  borderWidth: 1
              },
              {
                  label: "Batas",
                  data: tepatPulang,
                  fill:false,
                  borderColor: [                      
                      'rgba(255,99,132,1)',
                      'rgba(54, 162, 235, 1)',
                      'rgba(255, 206, 86, 1)',
                      'rgba(75, 192, 192, 1)',
                      'rgba(153, 102, 255, 1)'
                  ],
                  borderWidth: 1
              }]
          },
          options: {
            scales: {
              yAxes: [{
                ticks: {
                  userCallback: function(v) { 
                    return moment(new Date(v*1000).toISOString()).format("HH:mm")
                  }
                }
              }]
            },
            tooltips: {
              callbacks: {
                label: function(tooltipItem, data) {
                  return data.datasets[tooltipItem.datasetIndex].label + ': ' + (moment(new Date(tooltipItem.yLabel*1000).toISOString()).format("HH:mm"))
                }
              }
            }
          }
      });
      },      
      update_absen: function(){
        let d = moment().valueOf();
        this.$http.post(global_json.general_url+'/absen/update',{
          rfid:this.update_rfid,
          status:this.update_status,
          date_time:new Date().toISOString()
        }).then(function (data) {
          if(data.body.success == true){
            Swal({title:'Berhasil',
                text:JSON.stringify(data.body.data.message),
                type:'success',
                allowOutsideClick: false});                     
          }else if(data.body.success == false){            
            Swal({title:'Gagal',
                text:JSON.stringify(data.body.data.message),
                type:'error',
                allowOutsideClick: false});
          }
        });
      }
    }
  }
</script>

<style scoped>

</style>
