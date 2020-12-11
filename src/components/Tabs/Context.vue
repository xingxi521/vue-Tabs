<script>
export default {
    name:'Context',
    render() {
        return(
            <div>
                {
                    this.contentData
                }
            </div>
        )
    },
    props:['contentData']
}
</script>