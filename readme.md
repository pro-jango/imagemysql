#### PHP如何存储数据到数据库中
#### 字段类型选择
* 字段类型：longblob
```
获取图片内容
private function cutImage($imageInfo,$info,&$imageData){

	$imagePath = ".".$imageInfo["path"];
	$type = exif_imagetype($imagePath);
	$support_type = array(IMAGETYPE_JPEG , IMAGETYPE_PNG , IMAGETYPE_GIF);
	if(!in_array($type, $support_type,true)) {
		$imageData = "";
		return;
	}
	//Load image
	switch($type) {
		case IMAGETYPE_JPEG :
			$src_img = imagecreatefromjpeg($imagePath);
			break;
		case IMAGETYPE_PNG :
			$src_img = imagecreatefrompng($imagePath);
			break;
		case IMAGETYPE_GIF :
			$src_img = imagecreatefromgif($imagePath);
			break;
		default:
			$imageData = "";
			return ;
	}

	if(empty($src_img)){
		$imageData = "src_img error";
		return;
	}

	$inter_img = imagecreatetruecolor($info["width"] , $info["height"]);
	imagecopy($inter_img, $src_img, 0,0,$info["x1"],$info["y1"],$info["width"], $info["height"]);

	ob_start();
    imagejpeg($inter_img, NULL, 100);
    $final_image = ob_get_contents();
    ob_end_clean();
    $imageData =$final_image;
    return ;
}

//将数据存在到数据库：
$imageData = D("Questionlist")->getImage($id);
if(empty($imageData)){
    $this->success("错误：截取图片异常，请重新上传图片再尝试！");
}
$listModel->where(array('id'=>$id))->data(array('imagedata'=>$imageData))->save();


//图片显示
通过base64显示

public function getImageBase64($imageContent){
	$content = chunk_split(base64_encode($imageContent)); // base64编码
	$strBase64 = 'data:image/jpg;base64,' . $content;//合成图片的base64编码
	//注意，前面在生成图片内容时选择的函数是imagejpeg，所以这里的图片格式应该是image/jpg;
	return $strBase64;
}


//转换成base64，输出到图片的地址中。
$strBase64 = $this->getImageBase64($imageData);
echo "<img src='$strBase64'" />;

```

