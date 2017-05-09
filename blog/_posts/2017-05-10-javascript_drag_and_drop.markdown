---
layout: post
title:  "drag and drop을 이용하여 파일 업로드를 해보자"
date:   2017-05-10 08:47:05 +0900
categories: programming
tags: [programming javascript]
published: true
count: 125
descript: 'query의 외부 라이브러리가 아닌 drag, drop 이벤트를 활용하여 기능 구현해보기'
writer: 박정태
---

이번 포스팅에서는 서버 쪽이 아닌 프론트 단에서 파일 업로드를 하는 방법에 대해서 알아보려고 한다.

input에 type을 file로 하면 매우 쉽게 가능하지만 단순한 파일 선택이 아닌 로컬 컴퓨터에 존재하는 파일을 drag and drop을 이용하여 파일을 추가하는 방법에 대해서 알아보려고 한다.

우선 서버 쪽에서 파일을 처리하는 부분은 이전에 글을 작성한 적이 있으므로 이전 글을 참고하길 바란다.

[[node.js] multer 2 - array, field](http://blog.naver.com/pjt3591oo/220517951975)

[[node.js] multer를 이용한 파일 업로드 - single](http://blog.naver.com/pjt3591oo/220517017431)


```html
<form id="upload" action="/note/upload" method="POST" enctype="multipart/form-data">
  <fieldset>
    <div class="files">
      <input id="fileselect" type="file" name="fileselect" multiple="multiple" class="addFile"/>
    </div>
    <div id="filedrag">drag and drop</div>
    <div id="submitbutton">
      <button type="submit">Upload Files</button>
    </div>
  </fieldset>
</form>

<table class="table">
  <thead>
    <th>이름</th>
    <th>타입</th>
    <th>용량</th>
  </thead>
  <tbody id="list"></tbody>
</table>
```

필자는 files라는 속성을 찾는데 2시간가량이 걸렸던 것 같다... 파일 추가는 됐는데 자꾸 파일 객체가 추가가 안돼서 서버 측에서 파일을 제대로 받지 못하는 상황이 있었고 검색을 하다 보니 files라는 속성값이 있다는 것을 찾아냈다. 아래 스크립트는 파일을 drag and drop을 할 때마다 동적으로 파일을 추가하는 스크립트이다.
코드 자체에서 크게 어려운 점은 없다.

***drop 이벤트 발생 -> input 추가 -> 가장 마지막 input에 drop된 파일 객체 추가.***

```javascript
/**
 * Created by bagjeongtae on 2017. 5. 9..
 */

// getElementById
function $id(id) {
	return document.getElementById(id);
}

// output information
function Output(msg) {
	var m = $id("list");
	m.innerHTML = msg + m.innerHTML;
}

// call initialization file
if (window.File && window.FileList && window.FileReader) {
	Init();
}

// initialize
function Init() {

	var fileselect = $id("fileselect"),
		filedrag = $id("filedrag"),
		submitbutton = $id("submitbutton");

	// file select
	fileselect.addEventListener("change", FileSelectHandler, false);

	// is XHR2 available?
	var xhr = new XMLHttpRequest();
	if (xhr.upload) {

		// file drop
		filedrag.addEventListener("dragover", FileDragHover, false);
		// filedrag.addEventListener("dragleave", FileDragHover, false);
		filedrag.addEventListener("drop", FileSelectHandler, false);
		filedrag.style.display = "block";

		// remove submit button
		submitbutton.style.display = "none";
	}
}

// file drag hover
function FileDragHover(e) {
	e.stopPropagation();
	e.preventDefault();
	e.target.className = (e.type == "dragover" ? "hover" : "");
}

// file selection
function FileSelectHandler(e) {

	// cancel event and hover styling
    FileDragHover(e);

	// fetch FileList object
	var files = e.target.files || e.dataTransfer.files;

    console.log('te');
    addFileFromLastInput(files);

    // process all File objects
	for (var i = 0, f; f = files[i]; i++) {
		ParseFile(f);
	}
}

function ParseFile(file) {

    var fileName = file.name,
        fileType = file.type,
        fileSize = file.size + 'bytes';

	Output(
		"<tr><td>" + fileName +
		"</td><td>" + fileType +
		"</td><td>" + fileSize +
		"</td> </tr>"
	);
}

function addFileFromLastInput(file){

    var a = $('input#fileselect.addFile');
    a[a.length-1].files = file;

    try{
        let new_input = '<input id="fileselect" type="file" name="fileselect" multiple="multiple" class="addFile" />';
        $('.files').append(new_input);
    }catch(err){

    }

    return 0;
}
```

drop 이벤트를 통해서 해당 파일이 특정 dom 안에 떨어졌을 경우 이벤트를 잡을 수 있다.

```javascript
var files = e.target.files || e.dataTransfer.files;
```

drop된 객체에서 file 객체 가져오기

```javascript
var a = $('input#fileselect.addFile');
    a[a.length-1].files = file;
```

가장 마지막 input에 drop된 file 객체 추가

> css는 알아서 취향에 맞춰서 작성을 하면됩니다 ㅋㅋㅋ