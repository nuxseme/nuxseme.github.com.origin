---
title: cxsv源码分析系列（十一）挂件
date: 2016-06-29 22:31:11
categories: 
- cxsv源码分析
---
### 挂件
>挂件，可以自定义编辑内容和位置。在显示区的导航栏，专区等等这些承模块化的显示区域就可以用挂件来实现，满足用户频繁更新的需求(其实是程序员想省事)。

### 关键点
1 挂件并不是完全独立，切合整体的结构  
2 需要显示的页面有标记例如：{__widget__}  
3 输出页面的时候替换掉标记  
4 每个method对应到一个page_id  
5 后台能编辑存储挂件mongodb（page_id,content）

<!--more-->

### 重写display

>  
```
protected function display($tpl_file,$data=[]){
		if(!is_array($data)){
			_exit('template data must to be an arrays!');
		}
		$data = array_merge($this->data,$data);
		extract($data);
		ob_start();
		include($this->tpl($tpl_file));
		$content = ob_get_clean();
		// 如果有{__CONTENT__}，则获取模块并替换到页面
		if(strpos($content, '{__CONTENT__}')){
			$page_id = $this->page_id;
			$mdb = mongodb('widget_data');
			$widgets = $mdb->where("page_id={$page_id} and public_id='{$this->public['public_id']}' and theme='{$this->getTemplate()}'")->order('index asc')->select();
			$html = '';
			foreach ($widgets as $key => $widget) {
				if(!val($widget,'data') || time() - $widget['refersh_time'] > $widget['expired']){				
					ob_start();
					$error = false;
					try{
						(new $widget['widget_path']($this->page_id,$this))->show(['uniqid'=>$widget['_id']]);
					}catch(\Exception $e){
						$error = true;
						echo $e->getMessage();
					}
					$widget['data'] = ob_get_clean();
					if(!$error){
						$widget['refersh_time'] = time();
						$mdb->save($widget);
					}
				}
				$html .= '<div class="mod" data-uniqid="'.$widget['_id'].'" data-id="'.$widget['widget_id'].'" data-page-id="'.$widget['page_id'].'" data-name="'.$widget['tag'].'">'.val($widget,'data').'</div>' ;
			}
			$content = str_replace('{__CONTENT__}','<div class="mods">'.$html.'</div>',$content);
		}
		$this->data = [];
		echo $content;
	}
```

### 页面标记 
>  
 		</div>
		{__CONTENT__}
		<div class="indexMenuIcoContainer clearfix">
		<nav class="indexMenuIco clearfix">
		
### page_id

>  
		#在action的构造函数有对每一个action_url做冗余校验填充32位
		$this->act_name = \Lib\Common::getActionUrl();
		$this->page_id = abs(crc32($this->act_name));
		$this->assign('page_id',$this->page_id);




### 存储挂件

>  
```
public function saveData($data){
		if(!is_array($data)){
			return $this->action->jsonError('invalid data. the data must to be an arrays');
		}
		$mdb = mongodb('widget_data');
		$validator = new \Lib\Validator($data);
		$validator->setRules([
			'_id'=>['required'=>true],
			'name'=>['required'=>true,'validate'=>'strip_tags,trim'],
			'public_id'=>['default'=>$this->action->getPublic()['public_id'],'validate'=>'strip_tags,trim'],
			'theme'=>['required'=>true,'validate'=>'strip_tags,trim'],
			'tag'=>['required'=>true,'validate'=>'strip_tags,trim'],
			'page_id'=>['required'=>true,'validate'=>'longint'],
			'widget_id'=>['required'=>true,'validate'=>'absint'],
			'widget_path'=>['required'=>true,'validate'=>'trim'],
			'config'=>['required'=>true],
			'data'=>[],
			'refersh_time' => ['default'=>time()],
			'expired'=>['default'=>86400],
		])->setMessages([
			'_id'=>['required'=>'唯一ID不能为空'],
			'name'=>['required'=>'挂件名称不能为空','validate'=>'模块名称不合法'],
			'public_id'=>['validate'=>'模板ID不合法'],
			'theme'=>['required'=>'模板主题不能为空','validate'=>'模板主题不合法'],
			'tag'=>['required'=>'挂件标识不能为空','validate'=>'挂件标识不合法'],
			'page_id'=>['required'=>'页面ID不能为空','validate'=>'页面ID不合法'],
			'widget_id'=>['required'=>'挂件ID不能为空','validate'=>'挂件ID不合法'],
			'widget_path'=>['required'=>'挂件路径不能为空','validate'=>'挂件路径不合法'],
			'config'=>['required'=>'挂件配置不能为空'],
			'data'=>[],
		]);
		$data = $validator->validate();
		if(empty($data)){
			return $this->action->jsonError($validator->getError());
		}		
		if(!is_array($data['config'])){
			$data['config'] = json_decode($data['config'],true);
		}
		if(isset($data['data']) && !empty($data['data']) && !is_array($data['data'])){
			$data['data'] = json_decode($data['data'],true);
		}
		$condition = "_id='{$data['_id']}'";
		if($mdb->where($condition)->count()){
			return $this->action->jsonSuccess($mdb->where($condition)->update($data));
		}else{
			$data['index'] = $this->getAutoIncrementId($mdb);
			return $this->action->jsonSuccess($mdb->insert($data));
		}
	}
```


















