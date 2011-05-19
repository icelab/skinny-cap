#------ Settings ------------------------------

set :client_code, 'client-code'             # Client code, keep their jobs together
set :job_name,    'job-name'                # Job name
set :server_path, '/var/www/path-to-files'  # Path to your client files
set :user,        'username'                # User on the server
set :application, 'yourdomain.com'          # Domain/IP to connect to

#------ Server config -------------------------

set :deploy_to, "#{server_path}/#{client_code}/#{job_name}"
set :repository, '.'

# Load the default Capistrano recipes
load 'deploy'

# Override some paths
set :releases_path, deploy_to
set :current_dir, 'latest'

set :ssh_options, { :forward_agent => true } # Uses your public key for authentication
set :use_sudo, false

set :deploy_via, :copy
set :scm, :none
set :copy_compression, :gzip
set :copy_strategy, :export
set :copy_exclude, [".git", "Capfile", ".gitignore", "vendor", ".gitmodules", ".bundle", ".sass-cache", "Gemfile", "Gemfile.lock", ".rvmrc", ".Apple", ".DS_Store"]

role :app, application
role :web, application
role :db,  application, :primary => true

#------ Deployment tasks ---------------------

namespace :deploy do
  desc "Overides default setup task, create just a single directory"
  task :setup, :roles => :app do
    dirs = [deploy_to, releases_path]
    run "#{try_sudo} mkdir -p #{dirs.join(' ')} && #{try_sudo} chmod g+w #{dirs.join(' ')}"
  end
  
  desc "This overides the default :restart"
  task :restart, :roles => :app do
    # Do nothing but overide the default
    # If you need to restart and processes, add them here
  end

  task :finalize_update, :roles => :app do
    run "chmod -R g+w #{latest_release}" if fetch(:group_writable, true)
    # Overide the rest of the default method
  end
  
  #------ Rollback tasks [deploy:rollback] ---------------------
  
  namespace :rollback do
    desc "Points the current symlink at the previous revision. This is called by the rollback sequence, and should rarely (if ever) need to be called directly."
    task :revision, :except => { :no_release => true } do
      if previous_release
        run "rm #{current_path}; ln -s #{previous_release} #{current_path}"
      else
        abort "Could not rollback the code because there is no previous release"
      end
    end
    
    desc "Removes the most recently deployed release. This is called by the rollback sequence, and should rarely (if ever) need to be called directly."
    task :cleanup, :except => { :no_release => true } do
      run "if [ `readlink #{current_path}` != #{current_release} ]; then rm -rf #{current_release}; fi"
    end
    
    desc "Rolls back to the previously deployed version. The `current' symlink will be updated to point at the previously deployed version, and then the current release will be removed from the servers."
    task :code, :except => { :no_release => true } do
      revision
      cleanup
    end
    
    desc "Rolls back to a previous version and restarts. This is handy if you ever discover that you've deployed a lemon; `cap deploy:rollback' and you're right back where you were, on the previously deployed version."
    task :default do
      revision
      cleanup
    end
  end
end