
#关于在CI中进行多语言的方法

##概述
  CI框架本身自带了一个多语言的功能，但很多时候我们为了图方便，都没有真正使用到这个功能，而且如果是正式的多语言的，语言包的维护也会比较繁琐
  而且可能会造成翻译不完整的情况。
  
  现在我们采用一种通用的多语言的方法 i18n的国际化标准使用mo文件才存储我们的语言文件。
  
##第一部分 :PO,MO语言包文件

`PO` 是我们语言包的源文件

`MO` 是编译后的语言包文件，程序读取此文件
##第二部分 :CI中必备的helper
```
   <?php  if (!defined('BASEPATH')) exit('No direct script access allowed');
 
function getheaderlanguage()
{
 $langs = array();
 
 if (isset($_SERVER['HTTP_ACCEPT_LANGUAGE'])) {
     // break up string into pieces (languages and q factors)
     preg_match_all('/([a-z]{1,8}(-[a-z]{1,8})?)\s*(;\s*q\s*=\s*(1|0\.[0-9]+))?/i', $_SERVER['HTTP_ACCEPT_LANGUAGE'], $lang_parse);
 
    if (count($lang_parse[1])) {
        // create a list like "en" => 0.8
        $langs = array_combine($lang_parse[1], $lang_parse[4]);
     
        // set default to 1 for any without q factor
        foreach ($langs as $lang => $val) {
            if ($val === '') $langs[$lang] = 1;
        }

        // sort list based on value 
        arsort($langs, SORT_NUMERIC);
    }
 }
 
 // look through sorted list and use first one that matches our languages
   foreach ($langs as $lang => $val) {
	  if (strpos($lang, 'de') === 0) {
	   $language = "de";
	   return $language;
	  } else if (strpos($lang, 'en') === 0) {
	   $language = "en";
	   return $language;
	  } 
 	}
}

function get_translation_language($language_in_session){
 if(strlen($language_in_session) > 0)
 {  
  $language = $language_in_session;
 } 
 else
 {
  $language = getheaderlanguage();
 }

 if($language == "zh")
 {
  $locale = "zh_CH";  
 }
 else if($language == "en")
 {
  $locale = "en_US";  
 }
  else if($language == "tc")
 {
  $locale = "tc_CH";  
 }
 else
 {
  $locale = "zh_CH";
 }
    $lang_path = FCPATH.APPPATH.'language/locale';
    putenv('LANG='.$locale.'.UTF-8');
    bindtextdomain('itvalue', $lang_path);
    textdomain('itvalue');

 return $language;
}
?> 
```
##第三部分 :CI需要加载的语言切换

    
