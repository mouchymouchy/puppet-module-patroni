source ENV['GEM_SOURCE'] || 'https://rubygems.org'

def location_for(place_or_version, fake_version = nil)
  git_url_regex = %r{\A(?<url>(https?|git)[:@][^#]*)(#(?<branch>.*))?}
  file_url_regex = %r{\Afile:\/\/(?<path>.*)}

  if place_or_version && (git_url = place_or_version.match(git_url_regex))
    [fake_version, { git: git_url[:url], branch: git_url[:branch], require: false }].compact
  elsif place_or_version && (file_url = place_or_version.match(file_url_regex))
    ['>= 0', { path: File.expand_path(file_url[:path]), require: false }]
  else
    [place_or_version, { require: false }]
  end
end

ruby_version_segments = Gem::Version.new(RUBY_VERSION.dup).segments
minor_version = ruby_version_segments[0..1].join('.')

group :development do
  gem "voxpupuli-test", '9.2.1',        require: false
  gem "rubocop-performance", '~> 1.18', require: false
  gem "faraday", '~> 1.0',              require: false
  gem "github_changelog_generator",     require: false
  gem "puppet-blacksmith",              require: false
  gem "puppet-strings",                 require: false
  gem "coveralls",                      require: false
end
group :system_tests do
  gem "beaker", *location_for(ENV['BEAKER_VERSION'] || '~> 4.29')
  gem "beaker-abs", *location_for(ENV['BEAKER_ABS_VERSION'] || '~> 0.1')
  gem "beaker-pe",                                                               require: false
  gem "beaker-hostgenerator"
  gem "beaker-rspec"
  gem "beaker-docker"
  gem "beaker-puppet"
  gem "beaker-puppet_install_helper",                                            require: false
  gem "beaker-module_install_helper",                                            require: false
end

puppet_version = ENV['PUPPET_GEM_VERSION']
facter_version = ENV['FACTER_GEM_VERSION']
hiera_version = ENV['HIERA_GEM_VERSION']

gems = {}

gems['rake'] = [require: false]
gems['puppetlabs_spec_helper'] = [require: false]
gems['puppet'] = location_for(puppet_version)

# If facter or hiera versions have been specified via the environment
# variables

gems['facter'] = location_for(facter_version) if facter_version
gems['hiera'] = location_for(hiera_version) if hiera_version

gems.each do |gem_name, gem_params|
  gem gem_name, *gem_params
end

# Evaluate Gemfile.local and ~/.gemfile if they exist
extra_gemfiles = [
  "#{__FILE__}.local",
  File.join(Dir.home, '.gemfile'),
]

extra_gemfiles.each do |gemfile|
  if File.file?(gemfile) && File.readable?(gemfile)
    eval(File.read(gemfile), binding)
  end
end
# vim: syntax=ruby
