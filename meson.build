project('qemu', 'c', 'cpp')

git = find_program('git')
bash = find_program('bash')
fs = import('fs')

qemu_git_repo = 'https://github.com/qemu/qemu.git'
qemu_dir = meson.current_source_dir() / 'qemu'

if get_option('target_list') != []
    qemu_configure_arg_target_list = '--target-list=' + ','.join(get_option('target_list'))
else
    qemu_configure_arg_target_list = ''
endif

if get_option('enable_plugins')
    qemu_configure_arg_enable_plugins = '--enable-plugins'
else
    qemu_configure_arg_enable_plugins = ''
endif

if get_option('bin_dir') != ''
    qemu_configure_arg_bin_dir = '--bin-dir=' + get_option('bin_dir')
else
    qemu_configure_arg_bin_dir = ''
endif

if get_option('cc') != ''
    qemu_configure_arg_cc = '--cc=' + get_option('cc')
else
    qemu_configure_arg_cc = ''
endif

if get_option('cxx') != ''
    qemu_configure_arg_cxx = '--cxx=' + get_option('cxx')
else
    qemu_configure_arg_cxx = ''
endif

if get_option('extra_cflags') != []
    qemu_configure_arg_extra_cflags = '--extra-cflags=' + ' '.join(get_option('extra_cflags'))
else
    qemu_configure_arg_extra_cflags = ''
endif

if get_option('extra_cxxflags') != []
    qemu_configure_arg_extra_cxxflags = '--extra-cxxflags=' + ' '.join(get_option('extra_cxxflags'))
else
    qemu_configure_arg_extra_cxxflags = ''
endif

if get_option('extra_ldflags') != []
    qemu_configure_arg_extra_ldflags = '--extra-ldflags=' + ' '.join(get_option('extra_ldflags'))
else
    qemu_configure_arg_extra_ldflags = ''
endif

qemu_configure_args = []

if qemu_configure_arg_target_list != ''
    qemu_configure_args += [qemu_configure_arg_target_list]
endif

if qemu_configure_arg_enable_plugins != ''
    qemu_configure_args += [qemu_configure_arg_enable_plugins]
endif

if qemu_configure_arg_bin_dir != ''
    qemu_configure_args += [qemu_configure_arg_bin_dir]
endif

if qemu_configure_arg_cc != ''
    qemu_configure_args += [qemu_configure_arg_cc]
endif

if qemu_configure_arg_cxx != ''
    qemu_configure_args += [qemu_configure_arg_cxx]
endif

if qemu_configure_arg_extra_cflags != ''
    qemu_configure_args += [qemu_configure_arg_extra_cflags]
endif

if qemu_configure_arg_extra_cxxflags != ''
    qemu_configure_args += [qemu_configure_arg_extra_cxxflags]
endif

if qemu_configure_arg_extra_ldflags != ''
    qemu_configure_args += [qemu_configure_arg_extra_ldflags]
endif

if not fs.exists(qemu_dir)
    message('Checking out QEMU source code...')
    run_command(git, 'clone', qemu_git_repo, qemu_dir, check: true)
else
    message('Updating QEMU source code...')
    run_command(git, '-C', qemu_dir, 'pull', check: true)
endif

message('Configuring QEMU...')
message('Qemu args: ' + ' '.join(qemu_configure_args))
qemu_configure = run_command(
    bash,
    '-c',
    'cd ' + meson.current_build_dir() + ' && ' + (qemu_dir / 'configure') + ' '.join(qemu_configure_args) + '',
    check: true
)

target_bins = []

if get_option('target_list') == []
    target_bin_list = [
        'aarch64-softmmu',
        'alpha-softmmu',
        'arm-softmmu',
        'avr-softmmu',
        'cris-softmmu',
        'hppa-softmmu',
        'i386-softmmu',
        'loongarch64-softmmu',
        'm68k-softmmu',
        'microblazeel-softmmu',
        'microblaze-softmmu',
        'mips64el-softmmu',
        'mips64-softmmu',
        'mipsel-softmmu',
        'mips-softmmu',
        'nios2-softmmu',
        'or1k-softmmu',
        'ppc64-softmmu',
        'ppc-softmmu',
        'riscv32-softmmu',
        'riscv64-softmmu',
        'rx-softmmu',
        's390x-softmmu',
        'sh4eb-softmmu',
        'sh4-softmmu',
        'sparc64-softmmu',
        'sparc-softmmu',
        'tricore-softmmu',
        'x86_64-softmmu',
        'xtensaeb-softmmu',
        'xtensa-softmmu',
        'aarch64_be-linux-user',
        'aarch64-linux-user',
        'alpha-linux-user',
        'armeb-linux-user',
        'arm-linux-user',
        'cris-linux-user',
        'hexagon-linux-user',
        'hppa-linux-user',
        'i386-linux-user',
        'loongarch64-linux-user',
        'm68k-linux-user',
        'microblazeel-linux-user',
        'microblaze-linux-user',
        'mips64el-linux-user',
        'mips64-linux-user',
        'mipsel-linux-user',
        'mips-linux-user',
        'mipsn32el-linux-user',
        'mipsn32-linux-user',
        'nios2-linux-user',
        'or1k-linux-user',
        'ppc64le-linux-user',
        'ppc64-linux-user',
        'ppc-linux-user',
        'riscv32-linux-user',
        'riscv64-linux-user',
        's390x-linux-user',
        'sh4eb-linux-user',
        'sh4-linux-user',
        'sparc32plus-linux-user',
        'sparc64-linux-user',
        'sparc-linux-user',
        'x86_64-linux-user',
        'xtensaeb-linux-user',
        'xtensa-linux-user',
    ]
else
    target_bin_list = get_option('target_list')
endif

foreach target : target_bin_list
    if target.contains('-linux-user')
        tgt_name = 'qemu-' + target.split('-')[0]
    elif target.contains('-softmmu')
        tgt_name = 'qemu-system-' + target.split('-')[0]
    else
        error('Malformed target: ' + target)
    endif
    target_bins += [tgt_name]
endforeach

qemu_inc_dir = include_directories(qemu_dir / 'include' / 'qemu')
target_qemu_bins = custom_target(
    'qemu_bins',
    output: target_bins,
    command: [bash, '-c', 'cd ' + meson.current_build_dir() + ' && make -j`nproc`'],
)
