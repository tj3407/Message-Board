const express = require('express');
const mongoose = require('mongoose');
const path = require('path');
const bodyParser = require('body-parser');

const app = express();
app.use(bodyParser.urlencoded({ extended:true }));
app.use(express.static(path.join(__dirname, './static')));
app.set('views', path.join(__dirname, './views'));
app.set('view engine', 'ejs');

mongoose.connect('mongodb://localhost/messageboarddb');
var Schema = mongoose.Schema;
var PostSchema = new mongoose.Schema({
    name: {type: String, required: true},
    message: {type: String, required: true},
    comments: [{type: Schema.Types.ObjectId, ref: 'Comment' }]
}, {timestamps: true});

var CommentSchema = new mongoose.Schema({
    _post: {type: Schema.Types.ObjectId, ref: 'Post'},
    text: {type:String, required: true},
    name: {type:String, required: true}
}, {timestamps:true});

var Post = mongoose.model('Post', PostSchema);
var Comment = mongoose.model('Comment', CommentSchema);

app.get('/', function(req, res){
    Post.find({}).populate('comments').exec(function(err,posts){
        if(err){
            console.log('something went wrong');
        } else {
            console.log(posts);
            res.render('index', {posts: posts});
        }
    })
    
});

app.post('/process_post', function(req, res){
    var post = new Post({name: req.body.name, message: req.body.message});
    post.save(function(err){
        if(err){
            console.log('post not added to db');
        } else {
            res.redirect('/');
        }
    })
});

app.post('/post/:id', function(req,res){
    Post.findOne({_id: req.params.id}, function(err, post){
        var comment = new Comment({name: req.body.commenterName, text: req.body.comment});
        comment._post = post._id;
        console.log(post);
        comment.save(function(err){
            post.comments.push(comment);
            post.save(function(err){
                if(err){
                    console.log('comment not added to db');
                } else {
                    res.redirect('/');
                }
            })
        })
    })
});

app.listen(8000, function(){
    console.log('listening on port 8000')
});