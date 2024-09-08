<script>
    import PocketBase from 'pocketbase';

    const pb = new PocketBase('http://127.0.0.1:8090'); // Adjust the URL if needed
    let posts = [];
    let newPostContent = '';
    let errorMessage = '';

function logPost(post) {
        console.log('Post details:', 
            'ID:', post.id,
            'Content:', post.field || 'No content found',
            'Timestamp:', new Date(post.createdAt).toLocaleString()
        );
    }

async function fetchPosts() {
    try {
        const result = await pb.collection('posts').getFullList();
        console.log('API response:', result);
        posts = result;
        console.log('Posts:', posts);
    } catch (error) {
        console.error('Error fetching posts:', error);
    }
}

    async function createPost() {
        if (newPostContent.trim() === '') {
            errorMessage = 'Post content cannot be empty.';
            return;
        }
        
        try {
            await pb.collection('posts').create({
                field: newPostContent
            });
            newPostContent = ''; // Clear the input field
            errorMessage = '';
            fetchPosts(); // Refresh the posts list
        } catch (error) {
            console.error('Error creating post:', error);
            errorMessage = 'Failed to create post';
        }
    }

    fetchPosts();  // Fetch posts when the component loads
</script>

<main>
    <h1>Global Feed</h1>
    
    <!-- Post Creation Form -->
    <div>
        <h2>Create a Post</h2>
        <textarea bind:value={newPostContent} placeholder="Write your post here..."></textarea>
        <button on:click={createPost}>Submit Post</button>
        {#if errorMessage}
            <p style="color: red;">{errorMessage}</p>
        {/if}
    </div>
    
    <!-- Display Posts -->
    <div>
        <h2>Posts</h2>
        {#if posts.length > 0}
            <ul>
                {#each posts as post}
                    <li>{post.field || 'No content'}</li>
                {/each}
            </ul>
        {:else}
            <p>No posts available yet.</p>
        {/if}
    </div>

    {#each posts as post}
    <li>
        Post ID: {post.id}
        Content: {post.field || 'No content'}
        Timestamp: {new Date(post.createdAt).toLocaleString()}
        <button on:click={() => logPost(post)}>Log Post</button>
    </li>
{/each}
</main>

<style>
    textarea {
        width: 100%;
        height: 100px;
        margin-bottom: 10px;
    }
    button {
        padding: 10px;
        background-color: #007bff;
        color: white;
        border: none;
        border-radius: 5px;
        cursor: pointer;
    }
    button:hover {
        background-color: #0056b3;
    }
    p {
        margin: 0;
    }
</style>
