<?php

include 'functions.php';

$type = $_GET['type'];

$o = '';


/* header */

$o .= ' '.date('Y-m-d').' Ushio Report
';





$conn = db__connect("log");
$connAuth = db__connect("auth");

$data = db__getData($conn, "log_iis", "", "", "", "", "WHERE timestamp BETWEEN '".date('Y-m-d H:i:s', strtotime("-1 day"))."' AND '".date('Y-m-d H:i:s')."'");
$dataApi = db__getData($conn, "log_api", "", "", "", "", "WHERE timestamp BETWEEN '".date('Y-m-d H:i:s', strtotime("-1 day"))."' AND '".date('Y-m-d H:i:s')."'");

$o .= 'Domain Statistic    
';
$domain = Array();

foreach($data as $index=>$val){
    if(!array_key_exists($val['domain'], $domain)){
        $domain[$val['domain']] = Array();
    }
    array_push($domain[$val['domain']], $val);
}


foreach($domain as $index=>$val){
    $o .= $index.': 
';

    $totalSession = 0;
    $usrs = Array();
    foreach($val as $item){
        if($item['sessiontime']){
            $totalSession += $item['sessiontime'];
        }
        if(!in_array($item['fp'], $usrs)){
            array_push($usrs, $item['fp']);
        }
    }
    $o .= 'Visitors: '.count($usrs).' ';
    $o .= 'TSession: '.$totalSession;
    $o .= '
';
}



$o .= '
Visitor Statistic
    
';
$fp = Array();

foreach($data as $index=>$val){
    if(!array_key_exists($val['fp'], $fp)){
        $fp[$val['fp']] = Array();
    }
    array_push($fp[$val['fp']], $val);
}

foreach($fp as $index=>$val){


    $usrInfo = '';
    if(db__rowNum($connAuth, "fip", "fp", $index)){
        $res = db__getData($connAuth, 'fip', 'fp', $index);
        $token = $res[count($res)-1]['token'];
        $res = db__getData($connAuth, 'token', 'token', $token);
        if($res != 404 && $res[count($res)-1]['state']){
            $hash = $res[count($res)-1]['hash'];
            $res = db__getData($connAuth, 'account', 'hash', $hash);
            $usrInfo .= 'U'.$res[0]['uid'].' N: '.$res[0]['nickname'].' C: '.$res[0]['comments'].'
';
        }
    }


    $web = Array();
    $eeedog = array();
    $totalSession = 0;
    foreach($val as $item){
        if(!in_array($item['domain'], $web)){
            $web[$item['domain']] = 0;
        }
        if($item['domain'] == 'www.eee.dog'){
            $s = substr($item['url'], 19);
            $pos = strpos($s, '#');
            if($pos){
                $s = substr($s, 0, $pos);
            }
            $pos = strpos($s, '?');
            if($pos){
                $s = substr($s, 0, $pos);
            }
            if(!in_array($s, $eeedog)){
                array_push($eeedog, $s);
            }
        }
        if($item['sessiontime']){
            $web[$item['domain']] += $item['sessiontime'];
            $totalSession += $item['sessiontime'];
        }
    }
    if($usrInfo != "" || count($web) > 1 || $totalSession > 20){

    $o .= $index.': 
';
$o .= $usrInfo;
$o .= $val[0]['platform'].' ';
$o .= $val[0]['language'].' ';
$o .= $val[0]['timezone'].'
';
    $o .= 'web:';
    foreach($web as $d=>$session){
        $o .= $d.'('.$session.') ';
    }

    if(count($eeedog)){
        $o .= '
eeedog: ';
        foreach($eeedog as $url){
            $o .= $url .' ';
        }
    }

    $o .= ' 
';
    }
}





$o .= '
API Statistic    
';
$api = Array();

foreach($dataApi as $index=>$val){
    if(!array_key_exists($val['api'], $api)){
        $api[$val['api']] = Array();
    }
    array_push($api[$val['api']], $val);
}


foreach($api as $index=>$val){
    $o .= $index.': 
';
    $f = array();
    foreach($val as $item){
       $s = parse_url($item['_from'])['host']; 
        if(!array_key_exists($s, $f)){
            $f[$s] = 0;
        }
       $f[$s] ++;
    }
    $ip = array();
    foreach($val as $item){
       $s = $item['ip']; 
        if(!array_key_exists($s, $ip)){
            $ip[$s] = 0;
        }
       $ip[$s] ++;
    }
$o .= 'Num:'.count($val).' Host:'.count($f).' Usr:'.count($ip).'
';
    foreach($f as $usr=>$n){
        if($n>20){
            $o .= $usr.'('.$n.')';
        }
    }
    $o .= '
';

}





$o .= '------------------';

if($type == 'memobird') yimian__gugu($o);
elseif($type == 'email') yimian__mail('i@iotcat.me', date('Y-m-d').' Ushio Report', str_replace(PHP_EOL, '<br>', $o), 'Ushio-Report');
else echo str_replace(PHP_EOL, '<br>', $o);
