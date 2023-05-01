<script>
import axios from "axios";
const API_URL = import.meta.env.VITE_API_URL;
const API_TOKEN = import.meta.env.VITE_STRAPI_API_TOKEN;

export default {
    getPlots: function() {
        return axios.get(`${API_URL}/plots`)
    },
    createPlots: createPlots,
    
    createArea: function(draw, featureCollection) {
        if (featureCollection.features.length === 0) {
            return;
        }

        const plot = featureCollection.features[0];
        createPlots({
            mapbox_feature_id: plot.id,
            coordinates: plot.geometry.coordinates
        }).then( (response) => {
            const mapboxId = response.data.data.attributes.mapbox_feature_id;
            const strapiId = response.data.data.id;
            //remove drawn plot from map to properly add it back with BE properties
            draw.delete(draw.getSelected().features[0].id);
            draw.add({
                  type: "Feature",
                  geometry: { type: 'Polygon', coordinates: response.data.data.attributes.coordinates},
                  properties: { 
                        strapiId: strapiId,
                        mapboxId: mapboxId
                    }
                });
            this.$emit('apiResponded', {
                status: 'Success', 
                message: 'Plot was successfully created and persisted in database'
            });
        })
        .catch( err => {
            this.$emit('apiResponded', {
                status: 'Error',
                message: 'Error on creating plot',
                error: err
            })
        });
    },
    deletePlot: function(id) { 
        return axios.delete(`${API_URL}/plots/${id}`, {},  {
        headers: {
            Authorization: `Bearer ${API_TOKEN}`
            }
        });
    }
}

function createPlots(data) {
        return axios.post(`${API_URL}/plots`, { 
            data: data
        }, {
            headers: {
                Authorization: `Bearer ${API_TOKEN}`
            }
        })
    }
</script>