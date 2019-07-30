<?php

namespace App\Http\Controllers;

use App\Model\Student;
use Illuminate\Http\Request as req;
use DB;
class DayController extends Controller
{
    //渲染添加展示页
   function  add(req $req){
       $res=DB::table("che")->get();
      return  view("add",["res"=>$res]);
   }
   // 接取添加传过来的数据
   function  adddo(req $req){
       //接所有的值文本框值
        $res=$req->all();
        //
         $shenfen=$res['shenfen'];
        $session=$req->session();
        $session->put("uid",$shenfen);
        //实例化model
          $model=new Student();
          //model层sql语句
         $data= $model->ad($res);
         //判断成功跳展示页
        if ($data){
            return  redirect("show");
        }


   }


  function   show(req $req){

  //获取当前页
         $page=$req->post("page") ? $req->post("page"):1;
         //每页显示条数
         $size=4;
         //偏移量
         $pian=($page-1)*$size;

   //实例化模型
         $model=new Student();
         //求总页数
         $count=$model->count();
         //获取总条数
         $end=ceil($count/$size);
   //实现分页展示数据
         $limit=$model->lim($pian,$size);
        return   view("show",["res"=>$limit,"end"=>$end]);
  }


  function  fen(req $req){
  //接ajax传的id
       $id=$req->post("id");
       //接受搜索的值
       $sear=$req->post("sear");
      //接受搜索的值
      $sea=$req->post("sea");
      //分页当前页
      $page=$req->post("page") ? $req->post("page"):1;
      $size=4;
      $pian=($page-1)*$size;
      $sql=DB::table("student")->where("name","like","%$sear%")->where("sex","like","%$sea%")->get();
      $count=count($sql);
      $end=ceil($count/$size);
      //判断是否id为空  不为空删除
      if (!empty($id)){
         $res= DB::table("student")->where("id",$id)->delete();
        return  json_encode($res);
      }
      //搜索后分页
      $limit=DB::table("student")->where("name","like","%$sear%")->where("sex","like","%$sea%")->limit($size)->offset($pian)->get();
      return   json_encode(["res"=>$limit,"end"=>$end]);

  }

 //详情页
  function  xiang(req $req){
       //接受表单id
        $id=$req->get("id");
        //查询预约表的pid等于展示表的id一一对应
        $res=DB::table("yuyue")->where("pid",$id)->first();
        //渲染展示
        return  view("xiang",["res"=>$res]);

  }

}
