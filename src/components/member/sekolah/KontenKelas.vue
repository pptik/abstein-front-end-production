<template>
  <span>    
    <div class="ui top attached tabular menu">
      <a class="item" data-tab="first"><span class="grey-text">Visualisasi</span></a>
      <a class="active item" data-tab="second"><span class="grey-text">Tabulasi</span></a>      
      <a class="item" data-tab="thrid"><span class="grey-text">Rekapitulasi</span></a>      
      <a class="item" data-tab="fourth"><span class="grey-text">Tambah Kelas &amp; Siswa</span></a>
      <!-- <a class="item" data-tab="fifth"><span class="grey-text">Upload excel tambah Siswa &amp; RFID</span></a> -->
    <a class="item" data-tab="sixth"><span class="grey-text">Daftar Mesin</span></a>
    </div>
    <div class="ui bottom attached tab segment" data-tab="first">
      <div class="grey-text" style="font-size:xx-large;font-weight:300;margin-top:0.3em;">      
        Data Visualisasi bulan {{this_month}} {{this_year}}
      </div>
      <div class="ui form" style="margin-top:1em;">
          <div class="ui grid">
            <!-- <div class="eight wide column">
              <div class="field">
                <label>Pilih Sekolah</label>
                <select class="ui search dropdown" v-on:change="get_all_siswa(select_sekolah)" v-model="select_sekolah">
                  <option :value="sekolah._id" v-for="sekolah in daftarSekolah">{{sekolah.nama_sekolah}}</option>
                </select>
                
              </div>
            </div> -->
            
            <div class="ui two column grid">
              <div class="row">
                <div class="column">
                <span class="grey-text">Pilih Kelas</span>
                <select class="ui search dropdown" v-on:change="get_all_siswa_by_class(select_class)" v-model="select_class">
                  <option :value="kelas._id" v-for="kelas in daftarKelas">{{kelas.nama_kelas}}</option>
                </select> 
                </div>                
                  <div class="column">{{done_datang}}</div>
                          
              </div>
              <div class="row">
                <div class="column">
                <span class="grey-text">Pilih Siswa</span>
                <select class="ui search dropdown" v-on:change="get_absen(select_user_id)" v-model="select_user_id">
                  <option :value="siswa._id" v-for="siswa in daftarSiswa">{{siswa.profil.nama_lengkap}}</option>
                </select>     
                </div>
                <div class="column">{{done_pulang}}</div>  
              </div>              
            </div>            
          </div>                    
      </div> 
      <br/>  
      <canvas id="myChart" width="400" height="200"></canvas>
    </div>
    <div class="ui bottom attached active tab segment" data-tab="second">      
      <div class="grey-text" style="font-size:xx-large;font-weight:300;margin-top:0.3em;">      
        Data Tabulasi Hari Ini,  {{this_date}} {{this_month}} {{this_year}}
      </div>
      <div class="field" style="margin-top:1em;">                
        <label>Pilih Kelas</label>
        <select id="select-kelas" class="ui search dropdown" v-on:change="get_absen_kelas_harian()" v-model="select_class_rekapitulasi">
          <option :value="kelas._id" v-for="kelas in daftarKelasRekapitulasi">{{kelas.nama_kelas}}</option>
        </select>                   
      </div>
      <a href="#"id="button-export-table"  v-on:click.prevent="export_tabel(exportState)" style="margin-top:1em;"> <i class="download icon"></i> Unduh Data Absensi</a>       
     <table class="ui celled table grey-text" id="tabel-unduh-data-absensi-1">
        <!-- <caption>Absensi {{this_date}} {{this_month}} {{this_year}} </caption> -->
        <thead>
          <tr>
          <th style="text-align:center">No</th>          
          <th style="text-align:center">Nama Siswa</th>          
          <th style="text-align:center">Waktu</th>
          <th style="text-align:center">Status</th>
          <th v-if="$session.get('user_role') ===2" style="text-align:center">Aksi</th>
        </tr></thead>
        <tbody>
          <tr v-for="harian,index in sekolah_harian">
            <td style="text-align:center">{{index+1}}</td>
            <td>{{harian.nama_lengkap}}</td>            
            <td style="padding:0">
              <!-- <table style="width:100%;">
                <tr>
                  <td>
                    Datang
                  </td>                  
                  <td v-if="harian.waktu_datang == 0" bgcolor="red" class="white-text" style="text-align:center">
                    Alpa
                  </td>  
                  <td v-if="harian.waktu_datang != 0" bgcolor="green" class="white-text" style="text-align:center">
                    {{harian.waktu_datang}}
                  </td>                  
                </tr> 
                <tr>
                  <td>
                    Pulang
                  </td>                  
                  <td v-if="harian.waktu_pulang == 0" bgcolor="red" class="white-text" style="text-align:center">
                    Alpa
                  </td>  
                  <td v-if="harian.waktu_pulang != 0" bgcolor="green" class="white-text" style="text-align:center">
                    {{harian.waktu_pulang}}
                  </td>                  
                </tr>                
              </table> -->
              Datang:
              <span v-if="harian.waktu_datang == '0'" bgcolor="red" style="text-align:center">
                    <a class="ui red large label">Alpa</a>
              </span>  
              <span v-if="harian.waktu_datang != '0'" bgcolor="green" style="text-align:center">                
                <a class="ui green large label">{{harian.waktu_datang}}</a>
              </span>
              <br/>
              <br/>
              Pulang:
              <span v-if="harian.waktu_pulang == '0'" bgcolor="red" style="text-align:center">
                    <a class="ui red large label">Alpa</a>
              </span>  
              <span v-if="harian.waktu_pulang != '0'" bgcolor="green" style="text-align:center">
                <a class="ui green large label">{{harian.waktu_pulang}}</a>                
              </span>
            </td>
              <!-- <div class="ui list">
                <div class="item">
                  <div class="item">Datang:</div>
                  <span v-if="harian.waktu_datang == 0" class="ui red horizontal label">Alpa</span>
                  <span v-else-if="harian.waktu_datang != 0" class="ui green horizontal label">
                    {{harian.waktu_datang}}
                  </span>
                </div>
                <div class="item">
                  <div class="item">Pulang:</div>
                  <span v-if="harian.waktu_pulang == 0" class="ui red horizontal label">Alpa</span>
                  <span v-else-if="harian.waktu_pulang != 0" class="ui green horizontal label">
                    {{harian.waktu_pulang}}
                  </span>
                </div>                
              </div>  -->             
           <td v-if="harian.waktu_datang == 0 && harian.waktu_pulang == 0 " bgcolor="red" style="text-align:center"> 
              <span class="white-text">Alpa</span>            
            </td>
            <td v-if="harian.waktu_datang != 0 && harian.waktu_pulang != 0" bgcolor="green" style="text-align:center">  
              <span class="white-text">Hadir</span>            
            </td>            
            <td v-if="harian.status == 2" bgcolor="orange" style="text-align:center">  
              <span class="white-text">Izin</span>
            </td>
            <td v-if="harian.status == 3" bgcolor="purple" style="text-align:center">
              <span class="white-text">Sakit</span>
            </td>
            <td v-if="harian.waktu_datang != 0 && harian.waktu_pulang == 0" bgcolor="grey" style="text-align:center">                                                        
              <span class="white-text">Tidak Lengkap</span>  
            </td>
            <td style="text-align:center" v-if="$session.get('user_role') ===2">
              <a href="#" v-if="harian.rfid != 0" v-on:click="show_update_form_absent(harian.nama_lengkap,harian.waktu_datang,harian.waktu_pulang,harian.rfid)">Ubah Status</a>              
              <span v-else-if="harian.rfid == 0">RFID belum terdaftar</span>              
              <!-- 
                ubah_status_absen_form(harian._id,harian.rfid,harian.nama_lengkap)
                <div class="ui form">
                <div class="field">                  
                  <select class="ui search dropdown">
                    <option value="">Ubah Status</option>
                  </select>
                </div>
              </div> -->
            </td>
          </tr>          
        </tbody>
      </table>
    </div>    
    <div class="ui bottom attached tab segment" data-tab="thrid">             
      <!-- <div class="ui left icon input">
        <input type="text" placeholder="Pilih tanggal" id="datepicker">
        <i class="calendar alternate outline icon"></i>
      </div> -->
      <datepicker v-model="datepickerrekapitulasi" input-class="ui input" placeholder="Pilih tanggal" class="ui input" @closed="downloadRekapitulasi()"></datepicker>
       <button type="button" id="tombol-unduh-data-absensi"
                    style="margin-top:1em;background: linear-gradient(141deg, #2ecc71 10%, #27ae60 51%, #27ae60 75%);color:#FFFFFF;display:none;"
                    class="medium ui button button-submit" v-on:click.prevent="export_tabel_rekapitulasi">Unduh data absensi</button>
      <table class="ui celled table grey-text" id="tabel-unduh">
        <tr>
          <th>No</th>
          <th>Nama Siswa</th>
          <th>Kelas</th>
          <th>Waktu</th>
          <th>Status</th>
        </tr>
        <tr v-for="sharian,index in sekolah_harian_rekapitulasi">
          <td>{{index+1}}</td>
          <td>{{sharian.nama_lengkap}}</td>
          <td>{{sharian.nama_kelas}}</td>
          <td>
             Datang:
            <span v-if="sharian.waktu_datang == 0" bgcolor="red" style="text-align:center">
                  <a class="ui red large label">Alpa</a>
            </span>  
            <span v-if="sharian.waktu_datang != 0" bgcolor="green" style="text-align:center">                
              <a class="ui green large label">{{sharian.waktu_datang}}</a>
            </span>
            <br/>
            <br/>
            Pulang:
            <span v-if="sharian.waktu_pulang == 0" bgcolor="red" style="text-align:center">
                  <a class="ui red large label">Alpa</a>
            </span>  
            <span v-if="sharian.waktu_pulang != 0" bgcolor="green" style="text-align:center">
              <a class="ui green large label">{{sharian.waktu_pulang}}</a>                
            </span>
          </td>
           <td v-if="sharian.waktu_datang == 0 && sharian.waktu_pulang == 0 " bgcolor="red" style="text-align:center"> 
              <span class="white-text">Alpa</span>            
            </td>
            <td v-if="sharian.waktu_datang != 0 && sharian.waktu_pulang != 0" bgcolor="green" style="text-align:center">  
              <span class="white-text">Hadir</span>            
            </td>            
            <td v-if="sharian.status == 2" bgcolor="orange" style="text-align:center">  
              <span class="white-text">Izin</span>
            </td>
            <td v-if="sharian.status == 3" bgcolor="purple" style="text-align:center">
              <span class="white-text">Sakit</span>
            </td>
            <td v-if="sharian.waktu_datang != 0 && sharian.waktu_pulang == 0" bgcolor="grey" style="text-align:center">                                                        
              <span class="white-text">Tidak Lengkap</span>  
            </td>
        </tr>
      </table>
    </div>
    <div class="ui bottom attached tab segment grey-text" data-tab="fourth">     
      <div class="grey-text" style="font-size:xx-large;font-weight:300;margin-top:0.3em;">      
        Tambah Kelas
      </div>
      <br/>
      <form class="ui form">
        <div class="field">
          <label class="grey-text">Pilih Tahun Ajaran</label>
          <select class="ui search dropdown" v-model="create_class_tahun_ajaran">
            <option :value="tahun_ajaran._id" v-for="tahun_ajaran in create_class_tahun_ajaran_lists">{{tahun_ajaran.nama_tahun_ajaran}}</option>
          </select>                   
        </div>
        <div class="field">
          <label class="grey-text">Pilih Jurusan</label>
          <select class="ui search dropdown" v-model="create_class_jurusan">
            <option :value="jurusan._id" v-for="jurusan in create_class_jurusan_lists">{{jurusan.nama_jurusan}}</option>
          </select>                   
        </div>
        <div class="field">
          <label class="grey-text">Pilih Kelas</label>
          <select class="ui search dropdown" v-model="create_class_tingkat">
            <option :value="tingkat_kelas._id" v-for="tingkat_kelas in create_class_tingkat_lists">{{tingkat_kelas.nama_tingkat}}</option>
          </select>                   
        </div>
		<div class="field">
          <label class="grey-text">Nama Kelas</label>
          <input type="text" v-model="create_nama_kelas" placeholder="Contoh nama kelas: A"/>                 
        </div>
          <div class="ui two column grid">
            <div class="row">
              <div class="column">
                <div class="field">
                  <label class="grey-text">Jam Datang</label>
                  <input type="time" v-model="jamDatang" placeholder="Contoh nama kelas: A"/>                 
                </div>
              </div>
              <div class="column">
                <div class="field">
                  <label class="grey-text">Jam Pulang</label>
                  <input type="time" v-model="jamPulang" placeholder="Constoh nama kelas: A"/>                 
                </div>
              </div>
            </div>
          
        </div>
        	<br>
        <div class="field">
          <button type="button"
                  style="background: linear-gradient(141deg, #2ecc71 10%, #27ae60 51%, #27ae60 75%);color:#FFFFFF;"
                  v-on:click.prevent="createKelas()" class="huge ui button button-submit">Buat Kelas</button>          
          <!-- <router-link to="" class="white-text">Belum punya akun? Daftar disini</router-link> -->
        </div>
      </form>
      <br/>
      <br/>
      <div class="grey-text" style="font-size:xx-large;font-weight:300;margin-top:0.3em;">      
        Tambah Siswa
      </div>
      <br/>
      <form class="ui form">
        <div class="field">
          <label class="grey-text">Email</label>
          <input type="email" placeholder="Email" v-model="create_siswa_email"/>
        </div>
        <div class="field">
          <label class="grey-text">Username</label>
          <input type="text" placeholder="Username" v-model="create_siswa_username"/>
        </div>
        <div class="field">
          <label class="grey-text">Nama Lengkap</label>
          <input type="text" placeholder="Nama Lengkap" v-model="create_siswa_nama_lengkap"/>
        </div>
        <div class="field">
          <label class="grey-text">Jenis Kelamin</label>
          <select class="ui search dropdown" v-model="create_siswa_jenis_kelamin">
            <option value="M" >Laki-laki</option>
            <option value="F" >Perempuan</option>
          </select> 
        </div>
        <div class="field">
          <label class="grey-text">Sandi</label>
          <input type="password" placeholder="Sandi Akun Siswa" v-model="create_siswa_sandi"/>
        </div>
        <div class="field">
          <label class="grey-text">Kode RFID</label>
          <input type="text" placeholder="Kode RFID Absensi" v-model="create_siswa_kode_rfid"/>
        </div>
        <div class="field">
          <label class="grey-text">Kelas</label>
          <select class="ui search dropdown" v-model="create_siswa_kelas">
                  <option :value="kelas._id" v-for="kelas in daftarKelas">{{kelas.nama_kelas}}</option>
          </select>   
        </div>
        <div class="field">
          <button type="button"
                  style="background: linear-gradient(141deg, #2ecc71 10%, #27ae60 51%, #27ae60 75%);color:#FFFFFF;"
                  v-on:click.prevent="createSiswa()" class="huge ui button button-submit">Buat Siswa</button>          
          <!-- <router-link to="" class="white-text">Belum punya akun? Daftar disini</router-link> -->
        </div>
      </form>
     <!--  <h4 style="margin-top:2em;">Prosedur mendaftarkan Siswa</h4>
      <ol>
        <li>
          Apabila sudah membuat kelas dimana siswa terdaftar maka lewati langkah 1 dan langsung menuju langkah ke - 2. Daftarkan kelas dengan masuk dengan akun sekolah Anda di <a href="http://vidyanusa.id/public" target="_blank">vidyanusa.id</a>. Pilih menu kelas dan tekan tombol tambah kelas.
        </li>
         <li>
          Setelah memiliki kelas, maka Pilih Detail untuk menyalin kode kelas yang akan dimasukan ke dalam berkas excel(di jelaskan lebih detail pada poin ke- 3).
        </li> 
        <li>
          Siapkan berkas daftar siswa dengan contoh format seperti <a href="https://docs.google.com/spreadsheets/d/1epoQTKhcVN5ZXpESUxzoRfVP9QtsyXIbcxA7xflFr4c/edit?usp=sharing" target="_blank">ini</a>.
        </li>
        <li>
          Sesuaikan data sesuai dengan contoh berkas di poin 2
        </li>
        <li>
          Unggah berkas pada form di bawah ini:          
        </li>        
      </ol>      
      <form class="ui form" style="margin:1.5em;">            
        <div class="required field">
          <input type="file" accept=".xls, .xlsx" v-on:click.prevent="handleFileUpload()"/>
        </div>
        <div class="required field">
          <button type="button"
                  style="background: linear-gradient(141deg, #2ecc71 10%, #27ae60 51%, #27ae60 75%);color:#FFFFFF;"
                  class="medium ui button button-submit">Unggah</button>              
        </div>
      </form>            
      <h4 style="margin-top:2em;">Prosedur mendaftarkan RFID</h4>
      <ol>        
        <li>
          Siapkan berkas daftar daftar RFID dengan contoh format seperti <a href="https://docs.google.com/spreadsheets/d/1epoQTKhcVN5ZXpESUxzoRfVP9QtsyXIbcxA7xflFr4c/edit?usp=sharing" target="_blank">ini</a>.
        </li>
        <li>
          Sesuaikan data sesuai dengan contoh berkas di poin 1
        </li>
        <li>
          Unggah berkas pada form di bawah ini:          
        </li>        
      </ol>      
      <form class="ui form" style="margin:1.5em;">            
        <div class="required field">
          <input type="file" ref="file" accept=".xls, .xlsx" v-on:change="handleFileUpload()"/>
        </div>
        <div class="required field">
          <button type="button"
                  style="background: linear-gradient(141deg, #2ecc71 10%, #27ae60 51%, #27ae60 75%);color:#FFFFFF;"
                  class="medium ui button button-submit">Unggah</button>              
        </div>
      </form> -->
    </div> 
    <div class="ui bottom attached tab segment grey-text" data-tab="fifth">
    Cooming Soon :D
    </div>
      <div class="ui bottom attached tab segment grey-text" data-tab="sixth">
        <div class="ui two column grid">
          <div class="row">
          <div class="column">

        
              <br><br>
                 <div class="grey-text" style="font-size:xx-large;font-weight:300;margin-top:0.3em;">      
                   Tambah Mesin Abstein
                </div>
              <br/>
                <form class="ui form">
                  <div class="field">
                    <label class="grey-text">MAC Address</label>
                    <input type="email" placeholder="sample : 2x:3x:ex:4x:1x:9x" v-model="create_mac_address"/>
                  </div>
                  <div class="field">
                    <button type="button"
                            style="background: linear-gradient(141deg, #2ecc71 10%, #27ae60 51%, #27ae60 75%);color:#FFFFFF;"
                            v-on:click.prevent="regisMacAdders()" class="huge ui button button-submit">Simpan Address</button>          
                    <!-- <router-link to="" class="white-text">Belum punya akun? Daftar disini</router-link> -->
                  </div>
                </form>
                <br>
                <br>
         
        </div>
        <div class="column">

            <br><br>
              <table class="ui celled table grey-text" id="tabel-unduh-data-absensi-1">
        <!-- <caption>Absensi {{this_date}} {{this_month}} {{this_year}} </capton> -->
                <thead>
                  <tr>
                    <th style="text-align:center">No</th>          
                    <th style="text-align:center">Mac Address Yang Terdaftar</th>          
                   </tr>
                </thead>
                <tbody>
                  <tr v-for="address,index in macAddressData">
                    <td style="text-align:center">{{index+1}}</td>
                    <td>{{address}}</td>
                  </tr>        
                </tbody>
              </table>
              
        </div>
            </div>
        </div>
    </div>
    <modal name="form-update-absensi">            
        <p style="font-size:150%;padding:1em 1em 0em 1em">
          Form perbaharui status absensi                    
        </p>
        <table style="padding:0em 1em 0em 1em;">
          <tr>
            <td>Nama Siswa<td>
            <td>:</td> 
            <td>
              {{update_nama_siswa}}
            </td>
          </tr>
          <tr>            
            <td>Waktu Datang<td>
            <td>:</td> 
            <td>
              <span v-if="update_waktu_datang == 0" class="ui red horizontal label">Alpa</span>
              <span v-else-if="update_waktu_datang !=0" class="ui green horizontal label">{{update_waktu_datang}}</span>              
            </td>
          </tr>
          <tr>
            <td>Waktu Pulang</td>
            <td>:</td>
            <td>
              <span v-if="update_waktu_pulang == 0" class="ui red horizontal label">Alpa</span>
              <span v-else-if="update_waktu_pulang !=0" class="ui green horizontal label">{{update_waktu_pulang}}</span>              
            </td>
          </tr>
        </table>
        <form class="ui form" style="padding:1em;">      
          <div class="field">
            <label>Status</label>
            <select class="ui search dropdown" v-model="update_status">
              <option value="">Pilih Status</option>
              <option value="0">Alpa</option>
              <option value="1">Hadir</option>
              <option value="2">Izin</option>
              <option value="3">Sakit</option>
              <option value="4">Tidak Lengkap</option>
            </select>
          </div>
          <div class="required field">
            <button type="button"
                    style="background: linear-gradient(141deg, #2ecc71 10%, #27ae60 51%, #27ae60 75%);color:#FFFFFF;"
                    v-on:click.prevent="update_absen()" class="medium ui button button-submit">Perbaharui</button>
            <a href="#" v-on:click.prevent="close_update_form_absent()" target="_blank">Batal </a>          
          </div>
      </form>
    </modal>
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
    data(){
      return{
        chartLabel: [],
        exportState:'0',
        jamDatang:null,
        jamPulang:null,
        done_datang : null,
        done_pulang:null,
        //chartLabelY: ["07.00","08.00"]          
        waktuDatang: [],
        macAddressData:[],
        waktuPulang: [],
        tepatDatang: [],
        tepatPulang: [],
        daftarSekolah: [],
        sekolah_harian:[],
        daftarSiswa: [],
        daftarKelas: [],
        daftarKelasRekapitulasi: [],
        minDayEpoch: 0,
        maxDayEpoch: 0,
        select_user_id: null,
        select_sekolah: null,
        select_class: null,
        this_full_date: null,
        select_class_rekapitulasi: null,
        this_month: moment().format('MMMM'),
        this_year: moment().get('year'),
        this_date: moment().get('date'),
        this_class: null,
        update_nama_siswa: null,
        update_waktu_datang: null,
        update_waktu_pulang: null,
        update_rfid: null,
        update_status: null,
        select_date_get_absen: null,
        select_month_get_absen: null,
        monthCollections: [],
        image: null,
        datepickerrekapitulasi: null,
        daterekapitulasi: null,
        sekolah_harian: [],
        sekolah_harian_rekapitulasi: [],
        create_class_tahun_ajaran: null,
        create_class_jurusan: null,
        create_class_tingkat: null,
        create_class_tahun_ajaran_lists: [],
        create_class_jurusan_lists: [],
        create_class_tingkat_lists: [],
        create_siswa_email: null,
        create_mac_address:null,
        create_siswa_username: null,
        create_siswa_nama_lengkap: null,
        create_siswa_jenis_kelamin: null,
        create_siswa_sandi: null,
        create_siswa_kode_rfid: null,
        create_siswa_kelas: null,
		create_nama_kelas: null
        //datepickerrekapitulasi: new Date().toISOString()
      }
    },
    created() {
      this.check_session();
      //this.get_absen("5b834f3c6e33f31321bfaabd","5b83585226ebef39747eac4b");
      //this.get_all_siswa();
      this.get_all_kelas();
      this.get_mac_addressData()
      this.getLokalisasi();
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
      get_mac_addressData(){
        this.macAddressData=[]
        this.$http.post(global_json.general_url+'/absen/sekolah/getMacAdress',{
          sekolah:this.$session.get('id_sekolah')
        }).then(function (data,err){
              if(data.body.success == true){
                  var results = data.body.data
                    for(let counter=0;counter<results.length;counter++){
                this.macAddressData.push(results[counter].address)    
                console.log("Data Stringify : "+JSON.stringify(this.macAddressData))
              }  
              }
        })
      },
     
      createKelas: function(){
         Swal({
    //title: 'Processing...',
    html: 'Harap tunggu Data Sedang di proses...',
    allowOutsideClick : false,
    onOpen: () => {
      Swal.showLoading()
    },
  })
        this.$http.post(global_json.general_url+'/kelas/tambah',{
          tingkat:this.create_class_tingkat,
          jurusan:this.create_class_jurusan,
          tahun_ajaran:this.create_class_tahun_ajaran,
          sekolah:this.$session.get('id_sekolah'),
      nama_kelas: this.create_nama_kelas,
      jam_masuk:this.jamDatang,
      jam_pulang:this.jamPulang
        }).then(function (data,err) {
          if(data.body.success == true){ 
            Swal({
				title: "Berhasil",
				text: "Data kelas berhasil ditambahkan",
				type: "success",
				allowOutsideClick: true
			})
          }else{
          Swal({
				title: "Maaf",
				text: "Terjadi Kesalahan",
				type: "error",
				allowOutsideClick: false
			})
          }        
        })
      },
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
      export_tabel: function(State){
        if(State=='0'){
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

        this.exportState='1'
        }else{
          console.log("Data Export " +this.exportState)
        }
        
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
       getLokalisasi(){
           this.$http.post(global_json.general_url+'/absen/sekolah/absen/lokalisasisekolah',{
               _id:this.$session.get('id_sekolah')
           }).then(function (data,err) {
             console.log("Tanda : "+JSON.stringify(data))
           })
      },
      regisMacAdders(){
         Swal({
    //title: 'Processing...',
    html: 'Harap tunggu Data Sedang di proses...',
    allowOutsideClick : false,
    onOpen: () => {
      Swal.showLoading()
    },
  })
        this.$http.post(global_json.general_url+'/absen/sekolah/daftarMacAdress',{
          macAdress:this.create_mac_address,
          sekolah:this.$session.get('id_sekolah')
        }).then(function(data,err){
         if(data.body.success == true){ 
            Swal({
				title: "Berhasil",
				text: "Data kelas berhasil ditambahkan",
				type: "success",
				allowOutsideClick: true
      })
      this.macAddressData.push(this.create_mac_address)
          }else{
               Swal({
				title: "Maaf",
				text: "Terjadi Kesalahan",
				type: "error",
				allowOutsideClick: false
			})
          }
        })
      },
      cekData(){
          console.log("jam datang : "+this.jamDatang)
          console.log("jam pulang : "+this.jamPulang)

       var current_date = new Date();
                //stage 2
                const monthNames = ["January", "February", "March", "April", "May", "June",
                  "July", "August", "September", "October", "November", "December"
                ];

                var date = current_date.getDate();
                var month = current_date.getMonth(); //Be careful! January is 0 not 1
                var year = current_date.getFullYear();
                var dateString = date + " " +monthNames[month] + " " + year;
                
                //stage 3
             
                var eventMasuk = new Date(dateString+' '+this.jamDatang+' UTC+07:00');
                var eventPulang = new Date(dat)
                //stage 4  

                var dataJamMasuk = eventMasuk.toISOString();

          
     
          
          console.log("Year : "+dataJamMasuk)
      },
      get_absen(user){
        
        let vue = this
          vue.chartLabel.length=0;
                vue.waktuDatang.length=0; 
                vue.waktuPulang.length=0;
                vue.tepatDatang.length=0;
                vue.tepatPulang.length=0;
        console.log("chartLabel : "+JSON.stringify(vue.chartLabel))
      console.log("Data Id Absen : "+user)
      console.log("Data ID Sekolah : "+vue.$session.get('id_sekolah'))
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
              console.log("Starttime : "+moment().get('year')+"-"+parseInt(moment().get('month')+1)+"-01T00:00:00.000+0000")
              console.log("endtime : "+moment().get('year')+"-"+parseInt(moment().get('month')+2)+"-01T00:00:00.000+0000")
              if(results.length>0){
                //alert("data")
             
                for(let counter=0;counter<results.length;counter++){
                  console.log("XXI:"+moment(results[counter].tanggal_string).format('DD'))
                  vue.chartLabel.push(moment(results[counter].tanggal_string).format('DD'))
                 
                  if(vue.get_only_time(results[counter].max_kepulangan_string).getTime()/1000 <= vue.get_only_time(results[counter].pulang_string).getTime()/1000){
                      vue.waktuPulang.push(vue.get_only_time(results[counter].pulang_string).getTime()/1000)
                 
                    
                  }else{
                     vue.waktuPulang.push(vue.get_only_time(results[counter].max_kepulangan_string).getTime()/1000)

                    
                  }

                  if(vue.get_only_time(results[counter].max_kepulangan_string).getTime()/1000 <= vue.get_only_time(results[counter].datang_string).getTime()/1000){
                vue.waktuDatang.push(vue.get_only_time(results[counter].max_kedatangan_string).getTime()/1000);
                  
                  }else{
                   vue.waktuDatang.push(vue.get_only_time(results[counter].datang_string).getTime()/1000)

                    }

                  console.log("DATA PULANG: "+vue.get_only_time(results[counter].pulang_string).getTime()/1000)
                  console.log("DATA DATANG: "+vue.get_only_time(results[counter].datang_string).getTime()/1000)
                  console.log("DATA DATANG TEPAT: "+vue.get_only_time(results[counter].max_kedatangan_string).getTime()/1000)
                  console.log("DATA PULANG TEPAT: "+vue.get_only_time(results[counter].max_kepulangan_string).getTime()/1000)  

                  vue.tepatDatang.push(vue.get_only_time(results[counter].max_kedatangan_string).getTime()/1000);
                  vue.tepatPulang.push(vue.get_only_time(results[counter].max_kepulangan_string).getTime()/1000);
                }
              console.log("Data ChartLAbel After : "+JSON.stringify(vue.chartLabel))
                vue.june_graph();
                Swal.close()
              }else{
                Swal({title:'Error',
                text:'Data tidak tersedia Atau Mesin Absetein belum terdaftar',
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
            console.log("Reload : "+data.body.data.length)
            if(data.body.data.length >0){
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
            }else{
                Swal({title:'Maaf',
                text:"Result Data Tidak ditemukan",
                type:'error',
                allowOutsideClick: false})

                this.sekolah_harian=[]
            }
              //console.log("Harian kelas: "+JSON.stringify(data.body.data))
                                    
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
            console.log("ID KELAS : "+classe)
            if(data.body.success == true){
              vue.$http.post(global_json.general_url+'/absen/sekolah/getjamMP',{
                sekolah: this.$session.get('id_sekolah'),
                kelas:classe
              }).then(function(data){
                var maxDatang = data.body.data.jam_masuk.toString()
                var dateDatang = new Date(maxDatang)
                var doneDatang = "Batas Waktu Datang JAM "+moment(dateDatang).format('hh:mm a')

                this.done_datang = doneDatang

                var maxPulang = data.body.data.jam_pulang.toString()
                var datePulang = new Date(maxPulang)
                var donePulang = "Jam Kepulangan JAM "+moment(datePulang).format('hh:mm a')

                this.done_pulang = donePulang
              })
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
        console.log("tepat datang:"+tepatDatang)
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
