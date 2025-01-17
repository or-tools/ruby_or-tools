require "bundler/gem_tasks"
require "rake/testtask"
require "rake/extensiontask"

task default: :test
Rake::TestTask.new do |t|
  t.libs << "test"
  t.pattern = "test/**/*_test.rb"
end

Rake::ExtensionTask.new("or-tools") do |ext|
  ext.name = "ext"
  ext.lib_dir = "lib/or_tools"
end

task :remove_ext do
  path = "lib/or_tools/ext.bundle"
  File.unlink(path) if File.exist?(path)
end

Rake::Task["build"].enhance [:remove_ext]

task :update do
  require "digest"
  require "open-uri"
  require "tmpdir"

  version = "9.4.1874"
  distributions = ["x86_64_MacOsX-12.5", "arm64_MacOsX-12.5", "ubuntu-20.04", "ubuntu-18.04", "debian-11", "debian-10", "centos-8", "centos-7"]

  short_version = version.split(".").first(2).join(".")
  distributions.each do |dist|
    prefix = dist.include?("MacOsX") ? "" : "amd64_"
    filename = "or-tools_#{prefix}#{dist}_cpp_v#{version}.tar.gz"
    url = "https://github.com/google/or-tools/releases/download/v#{short_version}/#{filename}"
    dest = "#{Dir.tmpdir}/#{filename}"
    unless File.exist?(dest)
      temp_dest = "#{dest}.tmp"
      success = system("wget", "-O", temp_dest, url)
      raise "Download failed" unless success
      File.rename(temp_dest, dest)
    end
    puts "#{dist}: #{Digest::SHA256.file(dest).hexdigest}"
  end
end
