---
title: "handsome主题博客信息添加全站字数、在线人数、访客总数和网站加载耗时"
categories: [ "记录" ]
tags: [ "handsome" ]
draft: false
slug: "sidebarmodify"
date: "2022-07-23 17:18:00"
---

转载自：[Typecho博客handsome主题博客信息添加全站字数、在线人数、访客总数和网站加载耗时][1]


<!--more-->

首先将以下代码加到themes/handsome/libs/Content.php中放在class Content{}[tag type="danger"]之前[/tag]。
```
    /**
     * 全站字数
     */
    function allOfCharacters() {
        $chars = 0;
        $db = Typecho_Db::get();
        $select = $db ->select('text')->from('table.contents');
        $rows = $db->fetchAll($select);
        foreach ($rows as $row) { $chars += mb_strlen(trim($row['text']), 'UTF-8'); }
        $unit = '';
        if($chars >= 10000)     { $chars /= 10000; $unit = '万'; } 
        else if($chars >= 1000) { $chars /= 1000;  $unit = '千'; }
        $out = sprintf('%.2lf %s',$chars, $unit);
        return $out;
    } 
/**
     * 在线人数
     */
    function online_users() {
        $filename='online.txt'; //数据文件
        $cookiename='Nanlon_OnLineCount'; //Cookie名称
        $onlinetime=30; //在线有效时间
        $online=file($filename); 
        $nowtime=$_SERVER['REQUEST_TIME']; 
        $nowonline=array(); 
        foreach($online as $line){ 
            $row=explode('|',$line); 
            $sesstime=trim($row[1]); 
            if(($nowtime - $sesstime)<=$onlinetime){
                $nowonline[$row[0]]=$sesstime;
            } 
        } 
        if(isset($_COOKIE[$cookiename])){
            $uid=$_COOKIE[$cookiename]; 
        }else{
            $vid=0;
            do{
                $vid++; 
                $uid='U'.$vid; 
            }while(array_key_exists($uid,$nowonline)); 
            setcookie($cookiename,$uid); 
        } 
        $nowonline[$uid]=$nowtime;
        $total_online=count($nowonline); 
        if($fp=@fopen($filename,'w')){ 
            if(flock($fp,LOCK_EX)){ 
                rewind($fp); 
                foreach($nowonline as $fuid=>$ftime){ 
                    $fline=$fuid.'|'.$ftime."\n"; 
                    @fputs($fp,$fline); 
                } 
                flock($fp,LOCK_UN); 
                fclose($fp); 
            } 
        } 
        echo "$total_online"; 
    }      
    
    /**
     * 访客总数
     */
     function theAllViews(){
             $db = Typecho_Db::get();
             $row = $db->fetchAll('SELECT SUM(VIEWS) FROM `typecho_contents`');
                 echo number_format($row[0]['SUM(VIEWS)']);
     }

    /**
     * 加载耗时
     */
    function timer_start() {
        global $timestart;
        $mtime = explode( ' ', microtime()  );
        $timestart = $mtime[1] + $mtime[0];
        return true; 
    }
    timer_start();
    function timer_stop( $display = 0, $precision = 3  ) {
        global $timestart, $timeend;
        $mtime = explode( ' ', microtime()  );
        $timeend = $mtime[1] + $mtime[0];
        $timetotal = number_format( $timeend - $timestart, $precision  );
        $r = $timetotal < 1 ? $timetotal * 1000 . " ms" : $timetotal . " s";
        if ( $display  ) {
            echo $r;
        }
        return $r;
    }
```
themes/handsome/component/sidebar.php添加代码:
```
<li class="list-group-item text-second"><span class="blog-info-icons"><i data-feather="edit-3"></i></span><span class="badge pull-right"><?php echo allOfCharacters(); ?></span><?php _me("全站字数") ?></li>
<li class="list-group-item"> <i class="glyphicon glyphicon-user text-muted text-muted"></i> <span class="badge pull-right"><?php echo online_users() ?></span><?php _me("在线人数") ?></li>
<li class="list-group-item text-second"><span class="blog-info-icons"> <i data-feather="user"></i></span><span class="badge pull-right"><?php echo theAllViews();?></span><?php _me("访客总数") ?></li>
<li class="list-group-item text-second"><span class="blog-info-icons"> <i data-feather="clock"></i></span> <span class="badge pull-right"><?php echo timer_stop();?></span><?php _me("加载耗时") ?></li>
```
  [1]: https://www.cloudsd.com/archives/25.html