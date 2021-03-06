wlr_files += files(
	'direct-ipc.c',
	'noop.c',
	'session.c',
)

if host_machine.system().startswith('freebsd')
	wlr_files += files('direct-freebsd.c')
else
	wlr_files += files('direct.c')
endif

# logind

msg = []
if get_option('logind').enabled()
	msg += 'Install "lib@0@" or pass "-Dlogind=disabled".'
endif
if not get_option('logind').disabled()
	msg += 'Required for logind support.'
	msg += 'You may need to pass "-Dlogind-provider=elogind" or "-Dlogind-provider=systemd" to ensure the correct library is detected.'
endif

logind_version = '>=237'
logind_found = false

if get_option('logind-provider') == 'auto'
	if not get_option('logind').disabled()
		assert(get_option('auto_features').auto(), '-Dlogind-provider must be set to systemd or elogind since auto_features != auto')
		logind = dependency('libsystemd',
			required: get_option('logind'),
			not_found_message: 'libsystemd not found, trying libelogind\n' + '\n'.join(msg),
			version: logind_version,
		)
		if logind.found()
			features += { 'systemd': true }
		else
			logind = dependency('libelogind',
				required: get_option('logind'),
				not_found_message: 'libelogind not found\n' + '\n'.join(msg),
				version: logind_version,
			)
			if logind.found()
				features += { 'elogind': true }
			endif
		endif
		logind_found = logind.found()
	endif
else
	logind = dependency('lib' + get_option('logind-provider'),
		required: get_option('logind'),
		not_found_message: '\n'.join(msg).format(get_option('logind-provider')),
		version: logind_version,
	)
	if logind.found()
		features += { get_option('logind-provider'): true }
		logind_found = true
	endif
endif

if logind_found
	wlr_files += files('logind.c')
	wlr_deps += logind
endif

# libseat

libseat = dependency('libseat', required: get_option('libseat'), version: '>=0.2.0')
if libseat.found()
	wlr_files += files('libseat.c')
	wlr_deps += libseat
	features += { 'libseat': true }
endif
