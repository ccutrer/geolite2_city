require "bundler/gem_tasks"
require "rake/testtask"
require 'zlib'
require 'fileutils'
require 'rubygems/package'
require 'net/http'
require 'uri'

Rake::TestTask.new(:test) do |t|
  t.libs << "test"
  t.libs << "lib"
  t.test_files = FileList["test/**/*_test.rb"]
end

namespace :db do
  task :update do
    uri = URI('http://geolite.maxmind.com/download/geoip/database/GeoLite2-City.tar.gz')
    tarball = Net::HTTP.get(uri)
    z = Zlib::GzipReader.new(StringIO.new(tarball))
    unzipped = StringIO.new(z.read)

    FileUtils.mkdir_p(File.join(__dir__, 'db'))

    Gem::Package::TarReader.new(unzipped) do |tar|
      tar.each do |tarfile|
        if tarfile.directory?
          version = tarfile.full_name.split(/_/, 2)[1]

          File.open(File.join(__dir__, 'db', 'VERSION'), 'w+') do |f|
            f.write [version[0,4], version[4,2], version[6,2]].join('.')
          end

          next
        end

        filename = File.basename(tarfile.full_name)
        File.open(File.join(__dir__, 'db', filename), 'wb+') do |f|
          f.write(tarfile.read)
        end
      end
    end

  end
end

task :default => :test
