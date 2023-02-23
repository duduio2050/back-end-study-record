파일 저장 단계
    1. 전달 받은 파일 유무 확인
    
        if(!isset($_FILES['crop_img'])) throw new Exception("이미지 파일이 존재하지 않습니다.");

    2. 저장 할 폴더의 유무 확인

        if (!file_exists($save_path)) throw new Exception("폴더가 존재하지 않습니다");
            없는 경우 파일 생성
                mkdir($save_path, 0777);
                chown($save_path, 'nobody');
                chmod($save_path, 0777);

    3. 디렉터리 생성 경로 확인

        $save_path = $DOCUMENT_ROOT . 파일 경로;

    4. 파일 확장자, 파일 명 채취 ( 해당 기능은 기존 파일명에 _1 을 붙여야 하는 상황이기 떄문에 확장자와 파일명을 따로 생성 )
        
        // 파일명
        $withoutExtension = pathinfo($row[FILE_NAME], PATHINFO_FILENAME);
        // 확장자
        $fileInfo = pathinfo($row[FILE_NAME]);
        $ext = $fileInfo['extension'];

    5. 해당 이미지 확인 절차 ( 이미 생성 되어 있는 파일이 있는 지 확인 후 마지막 번호 +1 로 파일 생성 )
        
        for($i=1; $i<6; $i++){
            if (!file_exists($save_path.'/'. $withoutExtension . '_'.$i. '.'.$ext)) {
                // 파일 저장
                $saveFileName = $withoutExtension.'_'.$i.'.jpg';
                break;
            }
        }

    6. 동일한 파일명 체크

        if (file_exists($dest)) {
            throw new Exception("선택하신 파일과 동일한 이름의 파일이 이미 존재합니다.");
        }


    7. 파일 크기 체크

        if ($_FILES["crop_img"]["size"] > 1024 * 1024 * 10) {
            throw new Exception("10M 이하의 파일만 업로드 가능합니다.");
        }

    8. 파일 업로드 시도 // 실패 시 Exception

        if (!move_uploaded_file($cropImgFile, $dest)) {
            throw new Exception("파일을 지정한 디렉토리에 복사하는데 실패했습니다.");
        }

    9. DB 업데이트

파일 암호화 후 저장

    1. 유효성 검사 부분은 위와 동일

    2. 키 값으로 파일 암호화

        // $val = file_get_contents($imageData['tmp_name'])
        
		$key = $row[KEY];
		$output = openssl_encrypt($val, "aes-256-cbc", $key, true, str_repeat(chr(0), 16));
	
		$key = $row[KEY];
		$output = openssl_decrypt($val, "aes-256-cbc", $key, true, str_repeat(chr(0), 16));
	
    3. 파일 암호화 후 파일 open 후 암호화 데이터를 저장 후 닫기

        $file_handle = fopen($dest, 'w');
        fwrite($file_handle, $enc_image);
        fclose($file_handle);

    4. 암호화 파일을 다시 불러올 떄

        $file_contents = file_get_contents($dest);
		$file_contents = dec_card("key", $file_contents);
		$file_contents = base64_encode($file_contents);
		$file_contents = "data:image/jpeg;base64," . $file_contents;