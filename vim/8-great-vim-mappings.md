# [8 Great Vim Mappings](https://hashrocket.com/blog/posts/8-great-vim-mappings)

* Clone paragraph and paste it below (`noremap cp yap<S-}>p`)
* Align current paragraph (`noremap <leader>a =ip`)
  * leader is default as `\`
* Shift + Direction to change tabs
  * `noremap <S-l> gt`
  * `noremap <S-h> gT`
* Quit files with `\ + q`
  * `noremap <leader>q :q<cr>`
* Save files with `\ + s`
  * `noremap <leader>s : w<cr>
  * `inoremap <leader>s <C-c>:w<cr>`
