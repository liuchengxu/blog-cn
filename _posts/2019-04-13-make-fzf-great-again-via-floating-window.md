---

layout: post
title: 使用 NeoVim 的 floating window 让你再次爱上 fzf
tag: neovim
categories: posts
published: true

---

[fzf](https://github.com/junegunn/fzf) 是一个非常高效实用且美观的命令行工具，并且配置有对应的 vim 插件 [fzf.vim](https://github.com/junegunn/fzf.vim), 相信很多人都用过。这里就不在赘述，如果你还没有用过，很推荐一试。

自从 neovim 的浮动窗口 PR [https://github.com/neovim/neovim/pull/6619](https://github.com/neovim/neovim/pull/6619) 被合到 master 以后，很多插件都利用了这个特性实现了很多很酷的功能，比如 [coc.nvim](https://github.com/neoclide/coc.nvim)，另外 [vim-which-key](https://github.com/liuchengxu/vim-which-key) 和 [vista.vim](https://github.com/liuchengxu/vista.vim) 也利用了这一特性 。

浮动窗口的一个很大的特点就是不会像之前 split 的方式扰动你的窗口布局，晃动你的视线，而 fzf 也可以利用这一特性进一步提升体验！

比如下面这个效果图，我们可以让 fzf 在中间进行显示，有点类似于 IDEA 的搜索窗口：


![fzf](https://upload-images.jianshu.io/upload_images/127313-6347dafe4e907932.gif?imageMogr2/auto-orient/strip)

要实现上面的效果，需要配置 3 个地方。首先是 2 个配置项：

```vim
    " 让输入上方，搜索列表在下方
    let $FZF_DEFAULT_OPTS = '--layout=reverse'

    " 打开 fzf 的方式选择 floating window
    let g:fzf_layout = { 'window': 'call OpenFloatingWin()' }
```
还有 1 个函数指定如何打开浮动窗口：

```vim
function! OpenFloatingWin()
  let height = &lines - 3
  let width = float2nr(&columns - (&columns * 2 / 10))
  let col = float2nr((&columns - width) / 2)

  " 设置浮动窗口打开的位置，大小等。
  " 这里的大小配置可能不是那么的 flexible 有继续改进的空间
  let opts = {
        \ 'relative': 'editor',
        \ 'row': height * 0.3,
        \ 'col': col + 30,
        \ 'width': width * 2 / 3,
        \ 'height': height / 2
        \ }

  let buf = nvim_create_buf(v:false, v:true)
  let win = nvim_open_win(buf, v:true, opts)

  " 设置浮动窗口高亮
  call setwinvar(win, '&winhl', 'Normal:Pmenu')

  setlocal
        \ buftype=nofile
        \ nobuflisted
        \ bufhidden=hide
        \ nonumber
        \ norelativenumber
        \ signcolumn=no
endfunction
```

关于浮动窗口的更多信息，可以 `:help api-floatwin`.

另外，如果你的浮动窗口设置高亮无效，看看是否有设置  `g:fzf_colors`，这可能会重置浮动窗口的高亮，有浮动窗口的话就不用设置了。

因为还没有 release, 目前要体验这个特性的话需要自己从 neovim master 编译，macOS 用户直接 安装 `HEAD` 版本的 neovim 就行了。安装好 neovim，然后进行如上配置应该就可以了，对于 [https://github.com/liuchengxu/space-vim](https://github.com/liuchengxu/space-vim) 用户直接升级 space-vim 即可。
