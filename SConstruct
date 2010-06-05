# vim: ft=python expandtab

import os
from site_init import *

opts = Variables()
opts.Add(PathVariable('PREFIX', 'Installation prefix', os.path.expanduser('~/FOSS'), PathVariable.PathIsDirCreate))
opts.Add(BoolVariable('DEBUG', 'Build with Debugging information', 0))
opts.Add(PathVariable('PERL', 'Path to the executable perl', r'C:\Perl\bin\perl.exe', PathVariable.PathIsFile))

env = Environment(variables = opts,
                  ENV=os.environ, tools = ['default', GBuilder])
Initialize(env)

DBUS_VERSION="1.3.1"
env['DOT_IN_SUBS'] = {'@VERSION@' : DBUS_VERSION,
                      '@prefix@': env['PREFIX'],
                      '@exec_prefix@' : '${prefix}/bin', 
                      '@libdir@' : '${prefix}/lib', 
                      '@includedir@' : '${prefix}/include',
                      '@DBUS_SYSTEM_BUS_DEFAULT_ADDRESS@': 'tcp:host=localhost,port=12434',
                      '@EXPANDED_SYSCONFDIR@': '${prefix}/etc',
                      '@EXPANDED_DATADIR@': '${prefix}/data',
                      '@DBUS_DAEMONDIR@': '${prefix}/bin',
                      '@DBUS_CLIENT_LIBS@': '-lWS2_32 -lADVAPI32'
                      }
        
args = '-DCMAKE_INSTALL_PREFIX:PATH=%s' % (env['PREFIX'])

dlls = ['dbus-1.dll']
libs = ['dbus-1.lib']
exes = ['dbus-daemon.exe', 'dbus-test.exe', 'dbus-send.exe', 'dbus-launch.exe', 'dbus-monitor.exe', 'bus-test.exe', 'dbus-env.bat']

if env['DEBUG'] == 1:
    args += ' -DCMAKE_BUILD_TYPE:STRING=DEBUG'
    dlls = ['dbus-1d.dll']
    libs = ['dbus-1d.lib']


cmd = 'cmake -G "NMake Makefiles" ' + args + " cmake"
env.Alias("install", 
    env.Command(dlls + exes, ['#cmake/CMakeLists.txt'], '''
        %s
        nmake
        nmake install'''% (cmd)
    )
)

env.DotIn('dbus-1.pc', 'dbus-1.pc.in')
env.Alias("install", env.Install('$PREFIX/lib/pkgconfig', 'dbus-1.pc'))

headers = Split("\
	dbus.h		        \
	dbus-address.h		\
	dbus-bus.h		\
	dbus-connection.h	\
	dbus-errors.h		\
	dbus-macros.h		\
	dbus-memory.h		\
	dbus-message.h		\
	dbus-misc.h		\
	dbus-pending-call.h	\
	dbus-protocol.h		\
	dbus-server.h		\
	dbus-shared.h		\
	dbus-signature.h	\
	dbus-threads.h		\
	dbus-types.h		\
	dbus-arch-deps.h	\
        ")
services = 'debug-echo.service'
confs = ['session.conf', 'system.conf']

env['DOT_IN_SUBS']['@DLLS@'] = generate_file_element(dlls + exes, r'bin', env)
env['DOT_IN_SUBS']['@CONFS@'] = generate_file_element(confs, r'etc', env)
env['DOT_IN_SUBS']['@SERVICES@'] = generate_file_element(services, r'data/dbus-1/services', env)
env['DOT_IN_SUBS']['@HEADERS@'] = generate_file_element(headers, r'include/dbus-1.0/dbus', env)
env['DOT_IN_SUBS']['@LIBS@'] = generate_file_element(libs, r'lib', env)
env['DOT_IN_SUBS']['@PCS@'] = generate_file_element('dbus-1.pc', r'lib/pkgconfig', env)

env.DotIn('dbusrun.wxs', 'dbusrun.wxs.in')
env.DotIn('dbusdev.wxs', 'dbusdev.wxs.in')
env.Alias("install", env.Install('$PREFIX/wxs', ['dbusrun.wxs', 'dbusdev.wxs']))
