Basic CRUD actions (create, read, update, destroy) with ActiveRecord.

1. Create: Model.create (instantiates (Model.new) + persists to database (Model.save))
2. Read: Model.all / Model.find(id_number) 
3. Update: Model.update (always update from parent (has_many) Model so children are automatically updated, too)
4. Delete: Model.destroy

Create 
    get '/models/new' do
        erb :new
    end

    new.erb 
    => (contains form that posts to '/models')

    post '/models' do
        # extract data from params 
        Model.create(params)

        erb :show
    end

Read 
    # all instances 
    get '/models' do 
        @models = Model.all

        erb :index
    end

    # specific instances 
    get '/models/:id' do
        @model = Model.find(params[:id])

        erb :show
    end

Update 
    get '/models/:id/edit' do
        erb :edit
    end

    edit.erb 
    => (contains form for editing given instance of model)
    => (sends PATCH request to patch '/models/:id')

    patch '/models/:id' do
        Model.find(params[:id]).update.save
    end

NOTE: in order to allow app to send PATCH requests, need to do the following:
- add the following lines to config.ru 
    use Rack::MethodOverride
    run ApplicationController   
- add the following lines to patch form 
    <form action="/models/<%= @model.id %>" method="post">
        <input id="hidden" type="hidden" name="_method" value="patch">
        <input type="text" ...>
    </form>

MethodOverride is middleware that intercepts every request and looks for those with name="_method".
Changes the request to whatever value is found in value attribute, i.e., "patch".

Delete
    No page view. The "delete button" is a form that sends a DELETE request to =>
    delete 'models/:id/'
    => The form should appear as follows: 
    <form method="post" action="/models/<%= @model.id %>">
        <input id="hidden" type="hidden" name="_method" value="DELETE">
        <input type="submit" value="delete">
    </form>

    Hidden input field is how to submit PATCH and DELETE requests via Sinatra.
    