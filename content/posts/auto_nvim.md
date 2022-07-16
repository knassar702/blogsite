---
title: "Autocompletion for Neovim"
date: 2022-07-16T22:15:09+02:00
toc: false
images:
tags:
  - config
  - neovim
---
One of the coolest features that can make you more productive with NeoVim is autocompletion, so we have some plugins to choose between them based on the performance

* YouCompleteMe
* Coc.nvim

these plugins are great to use but they are very heavy because of Javascript and Python, so I decided to switch to another solution
and the solution is Lua Plugin called [nvim-cmp](https://github.com/hrsh7th/nvim-cmp)
with this plugin you can add an auto-completion to your neovim with minimal performance, all you have to do is download the **language server** 


## Install
before installing the plugins you need to install Lua first

```bash
$ sudo pacman -S lua
```

and you need a Plugin manager you can choose between 

* vim-plug ( I use this )
* Pathogen
* Vundle

after installing the nvim-cmp plugin you have to install some external plugins

```vim
" ~/.config/nvim/init.nvim
    Plug 'hrsh7th/cmp-nvim-lsp'
    Plug 'hrsh7th/cmp-buffer'
    Plug 'hrsh7th/cmp-path'
    Plug 'hrsh7th/cmp-cmdline'
    Plug 'hrsh7th/nvim-cmp'
    Plug 'hrsh7th/vim-vsnip'
```
after installing the server of your language you may need some Lua code to enable it 
so you can use this code to copy & paste into your **init.nvim** file
Also, Don't forget to check [this page](https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md) to get the right server for your language

in my case I'll install [rust-analyzer](https://rust-analyzer.github.io/)

```bash
$ sudo pacman -S rust-analyzer
```

```vim
" ~/.config/nvim/init.nvim
lua <<EOF
  -- Setup nvim-cmp.
  local cmp = require'cmp'
  cmp.setup({
    snippet = {
      -- REQUIRED - you must specify a snippet engine
      expand = function(args)
        vim.fn["vsnip#anonymous"](args.body) -- For `vsnip` users.
        -- require('luasnip').lsp_expand(args.body) -- For `luasnip` users.
        -- require('snippy').expand_snippet(args.body) -- For `snippy` users.
        -- vim.fn["UltiSnips#Anon"](args.body) -- For `ultisnips` users.
      end,
    },
    mapping = {
      ['<C-b>'] = cmp.mapping(cmp.mapping.scroll_docs(-4), { 'i', 'c' }),
      ['<C-f>'] = cmp.mapping(cmp.mapping.scroll_docs(4), { 'i', 'c' }),
      ['<C-Space>'] = cmp.mapping(cmp.mapping.complete(), { 'i', 'c' }),
      ['<C-y>'] = cmp.config.disable, -- Specify `cmp.config.disable` if you want to remove the default `<C-y>` mapping.
      ['<C-e>'] = cmp.mapping({
        i = cmp.mapping.abort(),
        c = cmp.mapping.close(),
      }),
      ['<CR>'] = cmp.mapping.confirm({ select = true }), -- Accept currently selected item. Set `select` to `false` to only confirm explicitly selected items.
    },
    sources = cmp.config.sources({
      { name = 'nvim_lsp' },
      { name = 'vsnip' }, -- For vsnip users.
      -- { name = 'luasnip' }, -- For luasnip users.
      -- { name = 'ultisnips' }, -- For ultisnips users.
      -- { name = 'snippy' }, -- For snippy users.
    }, {
      { name = 'buffer' },
    })
  })
  -- Set configuration for specific filetype.
  cmp.setup.filetype('gitcommit', {
    sources = cmp.config.sources({
      { name = 'cmp_git' }, -- You can specify the `cmp_git` source if you were installed it.
    }, {
      { name = 'buffer' },
    })
  })
  -- Use buffer source for `/` (if you enabled `native_menu`, this won't work anymore).
  cmp.setup.cmdline('/', {
    sources = {
      { name = 'buffer' }
    }
  })
  -- Use cmdline & path source for ':' (if you enabled `native_menu`, this won't work anymore).
  cmp.setup.cmdline(':', {
    sources = cmp.config.sources({
      { name = 'path' }
    }, {
      { name = 'cmdline' }
    })
  })
  -- Setup lspconfig.
  local capabilities = require('cmp_nvim_lsp').update_capabilities(vim.lsp.protocol.make_client_capabilities())
  -- Replace <YOUR_LSP_SERVER> with each lsp server you've enabled.
  require'lspconfig'.rust_analyzer.setup{
      capabilities = capabilities,
  }
EOF
```


You can scroll in the code suggestion with CTRL+B or CTRL+F


![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/eno0l4jdrk9v3yfa294r.png)
