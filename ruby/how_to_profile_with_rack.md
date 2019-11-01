    class ProfilerMiddleware  
      def initialize(app)
        @app = app
      end
    
      def call(env)
        if env["QUERY_STRING"].to_s.include?("stackprof")
          StackProf.start(mode: :wall, interval: Integer(ENV.fetch("STACK_PROF_INTERVAL"], "250"), raw: true)
        end
    
        result = @app.call(env)
    
        if env["QUERY_STRING"].to_s.include?("stackprof")
          StackProf.stop
          file_name = "/tmp/foo.txt"
          StackProf.results(file_name)
          result = [200, { "Content-Type" => "text/plain" }, [File.read(file_name)]]
        end
    
        result
      end
    end
