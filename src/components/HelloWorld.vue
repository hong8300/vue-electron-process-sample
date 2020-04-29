<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <v-img
          :src="require('../assets/logo.svg')"
          class="my-3"
          contain
          height="200"
        />
      </v-col>

      <v-col class="mb-4">
        <h1 class="display-2 font-weight-bold mb-3">
          Vue + Eletron - ChildProcess 
        </h1>
         <v-btn v-if="!is_run" color="normal" dark v-on:click="clear_data">画面クリア</v-btn><br/><br/>

        <!--        <H3>is_run {{ is_run }} </h3> -->
         <v-btn v-if="!is_run" color="warning" dark v-on:click="exec('python','--version')">実行: python --version</v-btn> 本番ではフルパスで指定しないと動きません。<br/><br/>
         <v-btn v-if="!is_run" color="warning" dark v-on:click="exec('ls','-l')">実行: ls -l</v-btn><br/><br/>
         <v-btn v-if="!is_run" color="warning" dark v-on:click="exec('pwd','')">実行: pwd</v-btn> 本番時とデバック時ではカレントディレクトリが違います。<br/><br/>
         <v-btn v-if="!is_run" color="warning" dark v-on:click="exec('/Applications/Visual\ Studio\ Code.app/Contents/Resources/app/bin/code','')">実行: VSCode 起動</v-btn>
         
        <pre align="left">[ {{ output_data }} ]</pre>

      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  export default {
    // コンポーネント名
    name: 'HelloWorld',

    // コンポーネントで使用するデータ
    data: function ()  {
      return { 
        output_data: "ここに実行結果がはいります。",
        is_run: false,
      }
    },

    // コンポーネントで使用する関数
    methods : {
      //
      clear_data : function() {
        this.output_data = "";
      },
      // 子プロセスを呼び出した結果を入れる
      exec_data : function(data) {
        console.log("function exec_data");
        console.log(data)
        this.output_data = data;
      },

      exec_error : function(data) {
        console.log("function exec_error");
        this.output_data = data;
        // 実行中フラグOFF
        this.is_run = false;
      },

      exec_exit : function(code) {
        if(code == 0){
          // 正常終了
          console.log("function exec: normal end");
        } else {
          // 異常終了（未実装）
          console.log("function exec: ERROR ");
        }
        // 実行中フラグOFF
        this.is_run = false;
      },

      // コプロ実行関数
      exec : function(cmd,param) {
        console.log("IN function exec")
        console.log(cmd,param)
        // 外部プロセス実行
        let childProcess = require("child_process");

        // ここに外部コマンドを記述
//        let p = childProcess.spawn('ls',['-lR']);
//        let p = childProcess.spawn('python',['--version']);
        let p;
        if ( param == "" ) {
          p = childProcess.spawn(cmd);
        } else {
          p = childProcess.spawn(cmd,[param]);
        }
        // プロセス実行中フラグ
        this.is_run = true;

        // UTF-8設定
        p.stdout.setEncoding("utf-8");

        // 出力を受け取る関数を踏力
        p.stdout.on("data", this.exec_data);

        // プロセスの実行失敗
        p.on("error", this.exec_error);

        // プロセス終了時のコールバック
        p.on("exit", this.exec_exit);

      }
    }
  }
</script>
