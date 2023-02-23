이미지 crop 직접 구현    
    1. 레거시 crop 기능 개발
    2. 새창에서 부모 창의 엘리먼트에 접근하여 이미지 데이터를 받아옴
    3. 이미지에서 크롭 하고 싶은 영역을 드래그
    4. 드래그 후 캔버스 생성
    5. 해당 이미지에서 x( 드래그 시작점의 이미지 x 좌표 ),y( 드래그 시작점의 이미지 y 좌표 ),width( 드래그 시작점으로부터 드래그한 영역의 가로 길이 ),height( 드래그 시작점으로부터 드래그한 영역의 세로 길이 ) 좌표 값 생성
    6. 캔버스를 드래그 width, height 값 으로 설정
    7. canvas.getContext('2d').drawImage 이미지 그리기
    8. 캔버스를 엘레멘트에 append ( append(canvas) 시 캔버스 태그로 추가 됨 )
    9. html2canvas 라이브러리를 사용하여 해당 캔버스의 이미지의 binary(base64 encoding) 값으로 파일을 만든다.
    10. 파일을 서버로 전달
    
    var SIZE, HEIGHT
    var CANVAS_SCROLL_X = 0, CANVAS_SCROLL_Y = 0
    $(document).ready(function(){
        opener.$('.appu_form_detail_chk').each(function(){
            if($(this).is(':checked')){
                let rcv_seq = $(this).val();
                $('input[name="rcv_seq"]').val(rcv_seq);
                $('#after_crop_img').attr('src', $(this).prev().find('img').attr('src'));
                let after_img = new Image();
                after_img.src = $(this).prev().find('img').attr('src');
                SIZE = after_img.width;
                HEIGHT = after_img.height;
            }
        });

        window.addEventListener('scroll', function(){
            CANVAS_SCROLL_Y = window.scrollY;
            CANVAS_SCROLL_X = window.scrollX;
        });

        var canvas = document.getElementById("bCanvas");
        canvas.width=SIZE;
        canvas.height=HEIGHT;
        var ctx = canvas.getContext('2d');

        ctx.lineWidth = 1;
        ctx.strokeStyle = "#ff0000";

        var sX,sY,cX,cY;
        var width, height;
        var canvasX = $("#bCanvas").offset().left;
        var canvasY = $("#bCanvas").offset().top;
        var draw = false;

        // click 시 draw 시작
        $("#bCanvas").mousedown(function(e){
            sX=parseInt((e.clientX-canvasX)+(CANVAS_SCROLL_X));
            sY=parseInt((e.clientY-canvasY)+(CANVAS_SCROLL_Y));
            draw = true;
        })
        $("#bCanvas").mousemove(function(e){
            if(draw){
                cX=parseInt(e.clientX-canvasX+(CANVAS_SCROLL_X));
                cY=parseInt(e.clientY-canvasY+(CANVAS_SCROLL_Y));
                ctx.clearRect(0,0,canvas.width,canvas.height); //clear canvas
                ctx.strokeRect(sX,sY,cX-sX,cY-sY);
            }
        })
        // 마우스 놓으면 rectangle 완성 및 popup 생성
        $("#bCanvas").mouseup(function(e){
            draw = false;

            // 일정 크기 이상의 사각형일때 crop
            var threshold = 10;
            if(Math.abs(cX-sX)>threshold&&Math.abs(cY-sY)>threshold){
                drawCanvas(sX,sY,cX-sX,cY-sY);
                $('#crop_img_body').css('width',cX-sX);
            }
        })

        $('#crop_img_submit').on('click', function (){
            let submit_crop_img = $("input[name='crop_img']").val();
            if(typeof submit_crop_img == "undefined" || submit_crop_img == "" || submit_crop_img == null){
                alert('crop 하실 이미지를 선택해주세요.');
                return false;
            }

            let object_file = dataURLtoFile($("input[name='crop_img']").val(),'test.jpeg')
            sendData(object_file, $('input[name="rcv_seq"]').val())
            // $('#crop_img_form').submit();
        })
    });

    function drawCanvas (x,y,width,height){
        $("#aDiv").children().remove();
        var canvas = document.createElement("canvas");

        var img = new window.Image();

        var w,h;

        if(Math.abs(width)<=Math.abs(height)){
            h = SIZE;
            w = h*width/height;
        } else {
            w = SIZE;
            h = w*height/width;
        }
        canvas.width = width;
        canvas.height = height;

        img.addEventListener("load", function () {
            var RATE = img.width/SIZE;
            canvas.getContext('2d').drawImage(img,
                x*RATE, y*RATE,
                width*RATE, height*RATE,
                0,0,
                width, height);
        },true);
        img.src = $('#after_crop_img').attr('src');
        $("#aDiv").append(canvas);
        // $("#aDiv").append('<img src="'+canvas.toDataURL()+'"/>');
    }

    function dataURLtoFile(dataurl, fileName) {

        var arr = dataurl.split(','),
            mime = arr[0].match(/:(.*?);/)[1],
            bstr = atob(arr[1]),
            n = bstr.length,
            u8arr = new Uint8Array(n);

        while(n--){
            u8arr[n] = bstr.charCodeAt(n);
        }

        return new File([u8arr], fileName, {type:mime});
    }

    function sendData(file, rcv_seq){
        let formData = new FormData();
        formData.append('crop_img', file);
        formData.append('rcv_seq', rcv_seq);
        $.ajax({
            url: '../../../ajax/b2c_form_img_crop.php',   // 호출할URL
            dataType:"JSON",                                           //데이터타입
            type:"POST",
            data:formData,
            enctype: "multipart/form-data",
            processData: false,
            contentType: false,
            success : function(response){
                alert('정상적으로 처리되었습니다.');
                opener.location.reload();
                window.close();
            },
            error:function(error) {
                console.log(error);
            }
        }); // ajax
    }

    $(function(){
        $("#btn_download").click(function (e) {
            let adiv = $('#aDiv').find('canvas');

            // let canvas = adiv.getElementsByTagName('canvas');
            html2canvas(adiv[0]).then(function (canvas) {
                var el = document.createElement("a")
                el.href = canvas.toDataURL("image/jpeg")
                // el.download = '이미지.jpg' //다운로드 할 파일명 설정
                // el.click()
                sendData(dataURLtoFile(el.href), $('input[name="rcv_seq"]').val())
            })
        })
    })