class Project:
    def __init__(self):
        self.build_squirrel()
        self.build_tinygettext()
        self.build_binreloc()
        self.build_supertux()

    def build_tinygettext(self):
        env = Environment(CPPPATH=["external/tinygettext/",
                                   "external/squirrel/include/",
                                   ".",
                                   "src"])
        env.ParseConfig("sdl-config --libs --cflags")
        self.libtinygettext = env.StaticLibrary("tinygettext", Glob("external/tinygettext/*.cpp"))

    def build_binreloc(self):
        env = Environment(CPPPATH=["external/binreloc/", "."])
        self.libbinreloc = env.StaticLibrary("binreloc", "external/binreloc/binreloc.c")

    def build_squirrel(self):
        env = Environment(CPPPATH=["external/squirrel/include/"])
        self.libsquirrel = env.StaticLibrary("squirrel",
                                             Glob("external/squirrel/squirrel/*.cpp") +
                                             Glob("external/squirrel/sqstdlib/*.cpp") +
                                             Glob("external/squirrel/sqstdlib/*.c"))

    def build_supertux(self):
        env = Environment(CPPPATH=["external/squirrel/include/",
                                   "external/",
                                   "src/",
                                   "."],
                          CXXFLAGS=["-O2", "-g3",
                                    "-ansi",
                                    "-pedantic",
                                    "-Wall",
                                    "-Wextra",
                                    "-Wnon-virtual-dtor",
                                    # "-Weffc++",
                                    # "-Wconversion",
                                    "-Werror",
                                    # "-Wshadow",
                                    "-Wcast-qual",
                                    "-Winit-self", # only works with >= -O1
                                    "-Wno-unused-parameter"],
                          LIBS=["GL", "physfs"])

        # Add libraries
        env.ParseConfig("sdl-config --libs --cflags")
        env.ParseConfig("pkg-config --libs --cflags openal")
        env.ParseConfig("pkg-config --libs --cflags vorbis vorbisfile ogg")
        env.Append(LIBS=[self.libsquirrel, self.libbinreloc, self.libtinygettext])
        env.Append(LIBS=["SDL_image"])
        env.Append(LIBS=["curl"])

        # Create config.h
        self.iconv_const = 0
        config_h = open('config.h', 'w')
        config_h.write('#define PACKAGE_NAME "Supertux"\n')
        config_h.write('#define PACKAGE_VERSION "Milestone 2"\n')
        config_h.write('#define ENABLE_BINRELOC 1\n')
        config_h.write('#define APPDATADIR "data"\n')
        config_h.write('#define HAVE_LIBCURL 1\n')
        config_h.write('#define HAVE_OPENGL 1\n')
        config_h.write('#define ICONV_CONST %s\n' % self.iconv_const)
        config_h.close()

        # base source
        supertux_sources = Glob("src/*.cpp") + Glob("src/*/*.cpp")

        # optional video drivers
        supertux_sources += Glob("src/video/gl/*.cpp")
        supertux_sources += Glob("src/video/sdl/*.cpp")
        
        env.Program("supertux", supertux_sources)

project = Project()

# EOF #