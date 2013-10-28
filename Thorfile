$LOAD_PATH.unshift(File.expand_path('../lib', __FILE__))

require 'thor'

require 'puppet_labs/webhook'
require 'puppet_labs/project'

class ProjectConfig < Thor
  namespace :projects

  desc "list", "List existing project configuration"
  def list
    PuppetLabs::Webhook.setup_environment(ENV['RACK_ENV'])

    projects = PuppetLabs::Project.all.map do |project|
      [
        project.id,
        project.full_name,
        project.jira_project,
        project.jira_labels,
        project.jira_components
      ]
    end

    projects.unshift TABLE_HEADER

    print_table projects
  end

  desc "create REPO_NAME JIRA_PROJECT", "Create a new project definition"
  method_option :jira_labels,     :type => :array, :default => []
  method_option :jira_components, :type => :array, :default => []
  def create(repo_name, jira_project)
    PuppetLabs::Webhook.setup_environment(ENV['RACK_ENV'])

    project = PuppetLabs::Project.new
    project.full_name    = repo_name
    project.jira_project = jira_project

    project.jira_labels     = options[:jira_labels]
    project.jira_components = options[:jira_components]
    project.save

    say "Successfully created new project."
    print_table [
      TABLE_HEADER,
      [
        project.id,
        project.full_name,
        project.jira_project,
        project.jira_labels,
        project.jira_components
      ]
    ]
  end

  desc "delete REPO_NAME", "Delete a project definition"
  def delete(repo_name)
    PuppetLabs::Webhook.setup_environment(ENV['RACK_ENV'])

    project = PuppetLabs::Project.find_by_full_name(repo_name)

    project.destroy
    say "Successfully deleted #{repo_name}"
  end

  def self.banner(task, namespace = true, subcommand = false)
    "#{basename} #{task.formatted_usage(self, true, subcommand)}"
  end

  TABLE_HEADER = [
    'ID',
    'Full name',
    'Jira project',
    'Jira labels',
    'Jira components'
  ]
end