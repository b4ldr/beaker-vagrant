require 'rspec/core/rake_task'

namespace :test do
  namespace :spec do
    desc 'Run spec tests'
    RSpec::Core::RakeTask.new(:run) do |t|
      t.rspec_opts = ['--color', '--format documentation']
      t.pattern = 'spec/'
    end

    desc 'Run spec tests with coverage'
    RSpec::Core::RakeTask.new(:coverage) do |t|
      ENV['BEAKER_TEMPLATE_COVERAGE'] = 'y'
      t.rspec_opts = ['--color', '--format documentation']
      t.pattern = 'spec/'
    end
  end

  namespace :acceptance do
    desc <<~EOS
      A quick acceptance test, named because it has no pre-suites to run
    EOS
    task :quick do
      # setup & load_path of beaker's acceptance base and lib directory
      beaker_gem_spec = Gem::Specification.find_by_name('beaker')
      beaker_gem_dir = beaker_gem_spec.gem_dir
      beaker_test_base_dir = File.join(beaker_gem_dir, 'acceptance/tests/base')
      load_path_option = File.join(beaker_gem_dir, 'acceptance/lib')

      sh('beaker',
         '--hosts', 'acceptance/config/nodes/redhat-nodes.yml',
         '--tests', beaker_test_base_dir,
         '--log-level', 'debug',
         '--load-path', load_path_option,
         '--keyfile', ENV['KEY'] || "#{ENV.fetch('HOME', nil)}/.ssh/id_rsa")
    end
  end
end

# namespace-named default tasks.
# these are the default tasks invoked when only the namespace is referenced.
# they're needed because `task :default` in those blocks doesn't work as expected.
task 'test:spec' => 'test:spec:run'
task 'test:acceptance' => 'test:acceptance:quick'

# global defaults
task test: 'test:spec'
task default: :test

begin
  require 'rubocop/rake_task'
rescue LoadError
  # RuboCop is an optional group
else
  RuboCop::RakeTask.new(:rubocop) do |task|
    # These make the rubocop experience maybe slightly less terrible
    task.options = ['--display-cop-names', '--display-style-guide', '--extra-details']
    # Use Rubocop's Github Actions formatter if possible
    task.formatters << 'github' if ENV['GITHUB_ACTIONS'] == 'true'
  end
end
