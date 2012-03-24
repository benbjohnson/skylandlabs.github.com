require 'fileutils'

#############################################################################
#
# GraphViz
#
#############################################################################

namespace :dot do
  task :generate do
    # Find each DOT file in dot/ directory.
    Dir.glob('dot/**/*.dot').each do |path|
      # Run graphviz over each one and move to the images/ directory.
      destination = path.sub(/^dot/, 'img')
      destination.sub!(/\.dot$/, '.png')
      
      FileUtils.mkdir_p(File.dirname(destination))
      
      sh "dot #{path} -Tpng -o #{destination}"
    end
  end
end
