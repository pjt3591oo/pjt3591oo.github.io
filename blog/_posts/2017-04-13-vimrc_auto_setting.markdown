---
layout: post
title:  "vim 각종 설정들로 부터 귀차니즘을 벗어나자"
date:   2017-04-13 08:26:05 +0900
categories: tools
tags: [tools, vim]
published: true
writer: pjt3591oo
count: 125
---

> 이번에는 유닉스 계열의 텍스트 에디터인 vim을 커스텀하여 개선을 해보고자 한다.

vim은 초심자가 적용하기가 쉽지않은 에디터다. 하지만 익숙해지면 굉장히 빨라진다
(라고 하지만 개인적으로 동의하지 않는 부분이다. 필자도 vim을 애용하지만 단지 IDE보다 가벼워서 사용하는 거지 개발 속도때문은 아니다)

vim은 `~/.vimrc`을 통해 귀찮은 설정들을 미리 해줄 수 있다.


```
:set nu
:set ts=4
```

해당 두 명령어는 필자가 가장 많이 설정을 하는 셋팅이다.
좌측에 line number를 표시해주는 것과 탭 간격을 4space로 바꾸어 주는 것이다.

하지만 파일을 닫고 다시 열면 다시 셋팅을 해주어야 하는 귀찮음이 있는데 ~/.vimrc에 추가를 해주면 매번 셋팅을 할 필요가 없다.

```bash
$ vi ~/.vimrc
:set nu
:set ts=4
```
위 처럼 작성을 하고 저장을 시킨 후 vim을 열면 해당 설정이 적용된 상태로 빔이 켜졌음을 확인 할 수 있다.

```.vim
set hlsearch " 검색어 하이라이팅
set nu " 줄번호
set autoindent " 자동 들여쓰기
set scrolloff=2
set wildmode=longest,list
set ts=4 "tag select
set sts=4 "st select
set sw=1 " 스크롤바 너비
set autowrite " 다른 파일로 넘어갈 때 자동 저장
set autoread " 작업 중인 파일 외부에서 변경됬을 경우 자동으로 불러옴
set cindent " C언어 자동 들여쓰기
set bs=eol,start,indent
set history=256
set laststatus=2 " 상태바 표시 항상
"set paste " 붙여넣기 계단현상 없애기
set shiftwidth=4 " 자동 들여쓰기 너비 설정
set showmatch " 일치하는 괄호 하이라이팅
set smartcase " 검색시 대소문자 구별
set smarttab
set smartindent
set softtabstop=4
set tabstop=4
set ruler " 현재 커서 위치 표시
set incsearch
set statusline=\ %<%l:%v\ [%P]%=%a\ %h%m%r\ %F\
" 마지막으로 수정된 곳에 커서를 위치함
au BufReadPost *
\ if line("'\"") > 0 && line("'\"") <= line("$") |
\ exe "norm g`\"" |
\ endif
" 파일 인코딩을 한국어로
if $LANG[0]=='k' && $LANG[1]=='o'
set fileencoding=korea
endif
" 구문 강조 사용
if has("syntax")
 syntax on
endif
" 컬러 스킴 사용
colorscheme jellybeans
```

위와같이 셋팅을 하면 기본적으로 빔을 통해 소스코드를 작성하기에 무리가 없을 것이다.

간혹 가다가 vimrc파일에 문제가 생겨서 vim을 실행 시킬떄 에러 문구가 뜰 수 있는데, 이떄는 당황하지 말고 vimrc파일을 다시 열어서 수정을 해주다.