IMAGE="ubuntu"
RVM_PATH="/usr/local/rvm"
SHARED_PATH=ENV["HOME"]
FOREMAN_ARGS="-f Procfile.thin web=1,worker=0,grunt=0"
PGUSER=ENV["PGUSER"]
PGPASS=ENV["PGPASS"]
PGHOST=ENV["PGHOST"]
PGPORT=ENV["PGPORT"]

task default: "run:core"

def dockercmd(image: IMAGE, wdir: "/app")
  <<-EOF.gsub(/\n?    /," ").strip
    docker run --rm -it
    -w #{wdir}
    --link tbk_db_1:postgres
    --volumes-from tbk_db_1
    --network docker_tbknet
    #{image}
  EOF
end

namespace :db do
  desc "Setup the databases"
  task :setup do
    cmd=""
    sql = <<-EOF.gsub(/      /,"")
      <<END
      DROP ROLE IF EXISTS #{PGUSER};
      CREATE ROLE #{PGUSER} SUPERUSER LOGIN ENCRYPTED PASSWORD \'#{PGPASS}\';
      END
    EOF
    basecmd = "exec psql -h #{PGHOST} -p #{PGPORT} -U postgres"
    psql = "sh -c \"#{basecmd} #{sql}\""
    puts "CMD: #{psql}"
    sh "#{cmd} #{psql}"
  end

  desc "Open a PSQL console"
  task :psql do
    cmd = "docker run -it --link tbk_db_1:postgres -v #{SHARED_PATH}:/shared --rm postgres:11.2"
    psql = "sh -c 'exec psql -h #{PGHOST} -p #{PGPORT} -U #{PGUSER}'"
    sh "#{cmd} #{psql}"
  end

  desc "Open a Bash shell to a db client"
  task :shell do
    sh "#{dockercmd(image: "postgres:11.2")} /bin/bash -l"
  end
end

namespace :run do
  desc "Run the core services"
  task :core do
    sh "docker-compose up --no-recreate -d apache db redis"
  end

  desc "Halt and destroy services"
  task :destroy do
    sh "docker-compose kill"
    sh "docker-compose rm -f"
  end
end

desc "Stop the core services"
task :stop do
  sh "docker-compose stop apache db redis"
end

desc "Get a shell into the container"
task :shell do
  sh "#{dockercmd} '/bin/bash -l'"
end
