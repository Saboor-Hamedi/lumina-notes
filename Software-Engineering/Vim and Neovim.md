---
id: a1b2c3d4-1199-4000-8000-000000000199
title: Vim and Neovim
language: markdown
tags: [software-engineering]
selection: null
isPinned: false
timestamp: 1781500001199
---
# Vim and Neovim

Vim is a highly configurable text editor built for efficient text editing. Neovim is a modern fork with better extensibility (Lua API) and async support.

## Modes

| Mode | Key | Description |
|------|-----|-------------|
| Normal | Esc | Navigate and manipulate text |
| Insert | i, a, o | Enter text |
| Visual | v | Select text (V=line, Ctrl+v=block) |
| Command | : | Execute commands |
| Replace | R | Overwrite text |

## Navigation

```vim
" Basic movement
h j k l      " Left, Down, Up, Right
w b          " Next/previous word
e            " End of word
0 $          " Start/end of line
^            " First non-whitespace character
gg G         " Start/end of file
H M L        " High/Middle/Low of screen
{ }          " Previous/next paragraph
Ctrl+d Ctrl+u " Page down/up
Ctrl+f Ctrl+b " Page forward/back

" Search
/pattern     " Search forward
?pattern     " Search backward
n N          " Next/previous match
* #          " Search word under cursor

" Jump list
Ctrl+o Ctrl+i " Jump back/forward
gf           " Go to file under cursor
gd           " Go to definition
```

## Editing

```vim
" Insert modes
i            " Insert before cursor
a            " Append after cursor
I            " Insert at line start
A            " Append at line end
o            " Open line below
O            " Open line above
s            " Delete char and insert
S            " Delete line and insert

" Change, delete, yank
ciw          " Change inner word
ci"          " Change inside quotes
ct.          " Change till period
di(          " Delete inside parentheses
da{          " Delete around braces
yi[          " Yank inside brackets

" Combining with motions
d3w          " Delete 3 words
c2j          " Change 2 lines down
y5l          " Yank 5 characters right
v3j          " Visual select 3 lines down
dG           " Delete to end of file
ggdG         " Delete entire file
```

## Registers

```vim
" Named registers
"ayw         " Yank word into register a
"ap          " Paste from register a

" Special registers
""           " Default (unnamed) register
"0           " Last yank (not affected by d/c)
"1 - "9      " Delete history (most recent in 1)
"+           " System clipboard
"*           " Selection clipboard
".           " Last inserted text
":           " Last command
"/           " Last search

" Clipboard integration
set clipboard=unnamedplus   " Use system clipboard
```

## Buffers, Windows, Tabs

```vim
" Buffers
:e file.txt   " Open file
:bnext        " Next buffer
:bprev        " Previous buffer
:bd           " Delete buffer
:ls           " List buffers
:b 3          " Go to buffer 3

" Windows (splits)
:vsplit       " Vertical split
:split        " Horizontal split
Ctrl+w v      " Vertical split shortcut
Ctrl+w s      " Horizontal split shortcut
Ctrl+w w      " Switch windows
Ctrl+w h/j/k/l " Navigate windows

" Tabs
:tabnew       " New tab
:tabnext      " Next tab
:tabprev      " Previous tab
gt            " Next tab
gT            " Previous tab
```

## Macros

```vim
" Record macro in register q
qq
    " Commands to record
    I- <Esc>    " Prepend "- "
    j           " Move down
q             " Stop recording

" Execute macro
@q            " Run macro once
@@            " Repeat last macro
10@q          " Run macro 10 times

" Edit macro
"qp          " Paste macro content, edit, "qdy$ to save
```

## Neovim Lua Configuration

```lua
-- ~/.config/nvim/init.lua

-- Options
vim.opt.number = true
vim.opt.relativenumber = true
vim.opt.tabstop = 4
vim.opt.shiftwidth = 4
vim.opt.expandtab = true
vim.opt.smartindent = true
vim.opt.wrap = false
vim.opt.ignorecase = true
vim.opt.smartcase = true
vim.opt.incsearch = true
vim.opt.hlsearch = true
vim.opt.termguicolors = true
vim.opt.undofile = true
vim.opt.scrolloff = 8
vim.opt.signcolumn = "yes"
vim.opt.updatetime = 250

-- Keymaps
vim.g.mapleader = " "

vim.keymap.set("n", "<leader>w", ":w<CR>")
vim.keymap.set("n", "<leader>q", ":q<CR>")
vim.keymap.set("n", "<leader>e", vim.diagnostic.open_float)
vim.keymap.set("n", "[d", vim.diagnostic.goto_prev)
vim.keymap.set("n", "]d", vim.diagnostic.goto_next)

-- Plugin manager (lazy.nvim)
local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
vim.opt.rtp:prepend(lazypath)

require("lazy").setup({
    -- Telescope (fuzzy finder)
    {
        "nvim-telescope/telescope.nvim",
        dependencies = { "nvim-lua/plenary.nvim" },
        config = function()
            local builtin = require("telescope.builtin")
            vim.keymap.set("n", "<leader>ff", builtin.find_files)
            vim.keymap.set("n", "<leader>fg", builtin.live_grep)
            vim.keymap.set("n", "<leader>fb", builtin.buffers)
            vim.keymap.set("n", "<leader>fh", builtin.help_tags)
        end
    },

    -- Treesitter (better syntax highlighting)
    {
        "nvim-treesitter/nvim-treesitter",
        build = ":TSUpdate",
        config = function()
            require("nvim-treesitter.configs").setup({
                ensure_installed = { "python", "javascript", "rust", "go", "lua" },
                highlight = { enable = true },
                indent = { enable = true },
            })
        end
    },

    -- LSP support
    {
        "neovim/nvim-lspconfig",
        config = function()
            local lspconfig = require("lspconfig")
            lspconfig.pyright.setup({})
            lspconfig.ts_ls.setup({})
            lspconfig.rust_analyzer.setup({})
            lspconfig.gopls.setup({})
        end
    },

    -- Autocompletion
    {
        "hrsh7th/nvim-cmp",
        dependencies = {
            "hrsh7th/cmp-nvim-lsp",
            "hrsh7th/cmp-buffer",
            "hrsh7th/cmp-path",
        },
        config = function()
            local cmp = require("cmp")
            cmp.setup({
                mapping = cmp.mapping.preset.insert({
                    ["<Tab>"] = cmp.mapping.select_next_item(),
                    ["<S-Tab>"] = cmp.mapping.select_prev_item(),
                    ["<CR>"] = cmp.mapping.confirm({ select = true }),
                }),
                sources = {
                    { name = "nvim_lsp" },
                    { name = "buffer" },
                    { name = "path" },
                },
            })
        end
    },

    -- Git integration
    { "lewis6991/gitsigns.nvim", config = true },

    -- File explorer
    {
        "nvim-tree/nvim-tree.lua",
        config = function()
            require("nvim-tree").setup()
            vim.keymap.set("n", "<leader>e", ":NvimTreeToggle<CR>")
        end
    },
})
```

**Links**: [[Agile Development]] | [[Clean Code Principles]] | [[Code Review Best Practices]] | [[Code Review Process]] | [[Coding Interview Patterns]] | [[Debugging Strategies]] | [[Developer Experience]] | [[Environment Variables]] | [[Error Handling Patterns]] | [[Estimation and Planning]] | [[Feature Flags and Toggles]] | [[GoF Design Patterns]] | [[Incident Response]] | [[Internationalization]] | [[Monorepo vs Polyrepo]] | [[Monorepo with Nx and Turborepo]] | [[Onboarding and Mentoring]] | [[Open Source]] | [[Performance Profiling]] | [[Programming Resources]] | [[Refactoring Techniques]] | [[Regular Expressions]] | [[Scrum Framework]] | [[Software Design Principles]] | [[SOLID Principles Deep Dive]] | [[System Design Interview]] | [[Technical Debt Management]] | [[Technical Writing]] | [[Unicode and Encoding]] | [[Virtualization]]
