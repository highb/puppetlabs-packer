PACKER_BIN = ENV['PACKER_BIN'] ||  File.expand_path('./packer')
OS_FAMILY = ENV['OS_FAMILY'] || 'centos'
OS_VERSION = ENV['OS_VERSION'] || '7.6'
ARCH = ENV['ARCH'] || 'x86_64'
ROLE = ENV['ROLE']
TEMPLATE = ENV['TEMPLATE'] || 'vmware.base.json'
TEMPLATE_PATH = ENV['TEMPLATE_PATH'] || File.expand_path("templates/common/#{TEMPLATE}")
ENV['PACKER_VM_OUT_DIR'] = ENV['PACKER_VM_OUT_DIR'] || File.expand_path("output/")

task default: %w[test]

task test: 'packer' do
  system "tests/test-syntax.sh"
end

task :packer do
  system "tests/test-setup.sh" unless File.exists? PACKER_BIN
end

task :clean do
  system "rm -f packer"
end

task :build do
  template_dir = File.expand_path("templates/#{OS_FAMILY}/#{OS_VERSION}/#{ARCH}")
  raise "Could not find template directory for #{OS_FAMILY} #{OS_VERSION}" unless File.exists? template_dir
  family_vars = File.expand_path("templates/#{OS_FAMILY}/common/vars.json")
  raise "Could not find common variables for #{OS_FAMILY}" unless File.exists? family_vars
  template_vars = File.expand_path("templates/#{OS_FAMILY}/#{OS_VERSION}/#{ARCH}/vars.json")
  raise "Could not find template variables for #{OS_FAMILY} #{OS_VERSION} " unless File.exists? template_vars

  var_files = ["--var-file #{family_vars}"]

  if (OS_FAMILY == 'centos' && ['5.11', '6.8', '6.9'].include?(OS_VERSION)) || OS_FAMILY == 'ubuntu'
    version_vars = File.expand_path("templates/#{OS_FAMILY}/#{OS_VERSION}/common/vars.json")
    raise "Could not find common variables for #{OS_FAMILY} #{OS_VERSION}" unless File.exists? version_vars
    var_files += ["--var-file #{version_vars}"]
  end

  var_files += ["--var-file #{template_vars}"]

  if ROLE
    role_vars = File.expand_path("templates/#{OS_FAMILY}/#{OS_VERSION}/#{ARCH}/#{ROLE}/vars.json")
    raise "Could not find role variables for #{OS_FAMILY} #{OS_VERSION} configured with #{ROLE}" unless File.exists? role_vars
    var_files += ["--var-file #{role_vars}"]
  end

  Dir.chdir(template_dir) do
    system "#{PACKER_BIN} build #{var_files.join(' ')} #{TEMPLATE_PATH}"
  end
end